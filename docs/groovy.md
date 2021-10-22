

## 调用api
```groovy
def get = new URL("http://106.14.240.65:8000/deploy?node=carApi").openConnection();
def getRC = get.getResponseCode();
def response = get.getInputStream().getText()
def robj = Eval.me(response)
return robj.join(',')

```


## 发布脚本

```groovy

pipeline {
    agent any
    environment {
            PowerShell = 'JenkinsFile\\web-site\\deploy.ps1'
            deploys =  "$params.deploys"
            projectKeys = "$params.projectKeys"
        }
    stages {
        stage('all'){
            steps{
                script{
                    script{

                        if("$params.password" != '!@#'){
                            println 'password error...........'
                            return
                        }
                        //获取部署信息  

                        def deployObj = [
                            'test' : [
                                'pwd' : "xxxxxxxxxxx",
                                'user' : 'op',
                                'dir' :  "E:\\\\testJenkins",
                                'host' : '127.0.0.1'
                            ],
                        ] 

                        // println params
                        

                        // deployObj.each{
                        //     println "$it.key :  $it.value.dir"
                        // }

                        def projectObj = readJSON text: '{}'
                        projectObj['b2b'] = 'test'
                        params.each{
                            if(it.key.startsWith('version_')){
                                if(it.value != "null"){
                                    def newKey = it.key.replace('version_','')
                                    projectObj[newKey] = it.value
                                }
                            }
                        }
                        println projectObj

                        def filePathPrefix = 'E:\\jenkins\\releases\\prod\\history\\'

                        // bat notifyCmd

                        bat 'cute s post -u "https://oapi.dingtalk.com/robot/send?access_token=xxxxxxxxx"  "{msgtype: \'text\', text: {content:\'publishing now:\\n ' + projectKeys +' @ ' + deploys + '\\n' + projectObj + '\'}}" '

                        println 'starting deploy'

                        //遍历所有部署
                        for(deploy in deploys.tokenize(',')){
                            if(deployObj[deploy] == null){
                                    println 'deployObj has no key :' + deploy
                                    continue
                            }
                            //获取服务器
                            for (project in projectKeys.tokenize(',')){
                                if(projectObj[project] == null){
                                    println 'projectObj has no key :' + project
                                    continue
                                }
                                
                                //println deploy
                                //println project

                                //文件发布到服务器
                                def fname = project + '_' + projectObj[project] + '.zip'
                                def filePath = filePathPrefix + fname

                                //copy file
                                bat 'if exist ".\\"' + fname+ ' ( del /f /q /s ".\\' + fname +'")'
                                bat 'copy /y ' + filePath + ' .\\'

                                sshPublisher(publishers: [sshPublisherDesc(configName: deploy, transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'echo 1', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '_packages', remoteDirectorySDF: false, removePrefix: '', sourceFiles: fname )], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

                                //deploy
                                def oneD = deployObj[deploy] 
                                //bak.bat  b20210630114400  d:\bushu  b2b
                                //publish.bat  b20210630114400  d:\bushu  b2b   xxx.zip  msg


                                def batchNo = 'b'+ projectObj[project]

                                //C:\\jenkins\\scriptsX\\bak.bat btest  E:\testJenkins b2b
                                def bakScript = 'C:\\jenkins\\scriptsX\\bak.bat ' + batchNo +'  ' + oneD.dir + ' ' + project

                                println 'bakScript : ' + bakScript
                                //C:\jenkins\scriptsX\publish.bat  btest  E:\testJenkins  b2b   b2b_test.zip  msg
                                def publishScript = 'C:\\jenkins\\scriptsX\\publish.bat  ' + batchNo+ '  ' + oneD.dir + '  ' + project + '   ' + fname + '  deploy_to_' + deploy

                                println 'publishScript : ' + publishScript
                                def psScript = '''$Username = \''''+ oneD.user  +'''\'
$Password = \'''' + oneD.pwd + '''\'
$pass = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $Username,$pass
try
{
    Invoke-Command -ComputerName ''' + oneD.host + ''' -ScriptBlock { ''' + bakScript + ''' } -credential $Cred
    Invoke-Command -ComputerName ''' + oneD.host + ''' -ScriptBlock { ''' + publishScript + ''' } -credential $Cred
}
catch
{
    Write-Host "远程调用失败："$Error[0]
    exit 1
} '''
                                println psScript
                                powershell psScript

                            }
                        }


                        bat 'cute s post -u "https://oapi.dingtalk.com/robot/send?access_token=xxxxxxxxxxxxxxxxxxxxxxx"  "{msgtype: \'text\', text: {content:\'publish ended\\ncheck deploy versionInfos @  http://xxxxx/infos.html \'}}" '

                    }
                }
            }
        }
    }
}


```