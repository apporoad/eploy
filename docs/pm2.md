### cli

```bash
pm2 start --name pnote aok -- api -s static -p 11540

pm2 save

pm2 startup

```

## xx.json like

```json
{
    "apps":[
        {
            "name"                  : "hubot",
            "cwd"                   : "/data/hubot",
            "args"                  : ["-n", "hubot", "-a", "bearychat"],
            "script"                : "bin/hubot",
            "node_args"             : [],
            "log_date_format"       : "YYYY-MM-DDTHH:mm:ssZ",
            "error_file"            : "/var/log/pm/hubot.err.log",
            "out_file"              : "/var/log/pm/hubot.log",
            "pid_file"              : "/var/run/hubot.pid",
            "exec_mode"             : "fork",
            "instances"             : 1,
            "min_uptime"            : "200s",
            "max_restarts"          : 0,
            "max_memory_restart"    : "150M",
            "watch"                 : true,
            "ignore_watch"          : ["[\\/\\\\]\\./", "node_modules"],
            "merge_logs"            : true,
            "exec_interpreter"      : "bash",
            "autorestart"           : true,
            "vizion"                : false,
            "env": {
                "HUBOT_BEARYCHAT_TOKENS"               : "4508429262769561a586f5ec626939c6",
                "HUBOT_BEARYCHAT_MODE"      : "rtm",
                "EXPRESS_PORT"                   : "11542"
            }
        }
    ]
}
```

```bash
pm2 start xx.json
```

