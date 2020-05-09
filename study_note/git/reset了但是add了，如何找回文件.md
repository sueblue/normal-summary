手贱操作：git reset --hard sha1恢复时，把add的内容直接覆盖掉,找不到代码了。

因为只有add，git reflog无用，git fsck --lost-found 里边也找不到个啥。

ps:至今未找到.git/lost-found/other 文件夹在哪。真的奇了怪了。

恢复方法：

```

find .git/objects -type f 

```
出现以下内容

```
-r--r--r-- 1 sssfff 1049089     297 5月   9 14:40 .git/objects/14/6d06f321393f8516bc8706582d3eb0bba6f6aa
-r--r--r-- 1 sssfff 1049089     298 5月   9 14:40 .git/objects/18/598b6f1900f7702bf01ae8c4598487d2775e39
-r--r--r-- 1 sssfff 1049089     868 5月   9 14:40 .git/objects/1f/85b7eb25d2d99e4c6a4a03ce868bd3b574d8ed
-r--r--r-- 1 sssfff 1049089      72 5月   9 14:40 .git/objects/1c/cfe7cb022a03df2c53e54ab9bbfffb8285a5d3
-r--r--r-- 1 sssfff 1049089     238 5月   9 14:40 .git/objects/17/071e43a3d821026a9600006d42dedfc47011d0
-r--r--r-- 1 sssfff 1049089    2574 5月   7 17:23 .git/objects/08/69c5f92dbb71bb6bc12ff9850d94ce395dfee2
-r--r--r-- 1 sssfff 1049089    1623 5月   7 17:23 .git/objects/23/bfb3dd4a2de0f6f62fc819e4c15c4d25d1580d
-r--r--r-- 1 sssfff 1049089     703 5月   7 17:23 .git/objects/13/ddb318becf57f02c9946bf40fad9e8c99f42f3
-r--r--r-- 1 sssfff 1049089     237 5月   7 17:20 .git/objects/2e/33878c9b51ecc668cca496fbcc3cf85f972bc7
```
使用 git cat-file -p sha1 --读取内容

git cat-file -p 2e33878c9b51ecc668cca496fbcc3cf85f972bc7

sha1 的值为object后边的
/2e/33878c9b51ecc668cca496fbcc3cf85f972bc7=》2e33878c9b51ecc668cca496fbcc3cf85f972bc7

这时候就出现代码内容：
```
dddd test
```
手动去找麻烦：
此命令将所有flie sha1值都存入obj文件里
```
find .git/objects -type f | xargs ls -lt | sed 136q | awk '{gsub(".git/objects/", "", $9);gsub("/","",$9);print $9}' | tee obj
```
然后使用nodejs脚本跑一下git命令
```
var fs = require("fs");
let child_process = require('child_process');

const argv = process.argv

if (argv.length <= 2) {
    console.log("请指定目标地址!--->例如:node gitpull.js 'D:\\xxx\\xxxx' ")
    return
}
const githref = argv[2]

var data = fs.readFileSync('obj');
var arr = data.toString().split(/[\s\n]/);
for (let i = 0; i < arr.length - 1; i++) {
    let cmdstr = `git cat-file -p ${arr[i]} > flie-${arr[i]}`;
    
    child_process.exec(cmdstr, { cwd: githref }, function (error, stdout, stderr) {
        if (error !== null) {
            console.log('exec error: ' + error);
        } else {
            console.log(arr[i] + ' ' + stdout)
            // console.log(stdout)
        }
    });
}

console.log("程序执行完毕。");
```
简陋版本缺点：

所有的生成的文件都会在项目文件夹下边。

用vscode全局搜索文件找到丢失的文件，然后利用git checkout 该删除的就删除。

参考：

http://www.ai-freedom.org/2018/06/09/git_reset_hard_disaster/#comments
https://www.cnblogs.com/justyouadmin/p/11395839.html