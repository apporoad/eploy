## git 私服

```bash
export current_projet_path=/data/git

git init --bare ${current_projet_path}/yourPorject.git

cd ${current_projet_path}
chown -R apporoad:apporoad yourPorject.git/

git clone apporoad@holyond.top:/data/git/yourPorject.git

```