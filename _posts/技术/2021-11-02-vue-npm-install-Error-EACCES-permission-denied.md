# vue执行npm install报Error: EACCES: permission denied, mkdir ‘......node-sass/build‘错误解决方案

```
gyp ERR! configure error 
gyp ERR! stack Error: EACCES: permission denied, mkdir '/Users/zhang/Documents/data_insight_yinzhou/data_insight_yinzhou/node_modules/node-sass/build'
gyp ERR! System Darwin 17.6.0
gyp ERR! command "/usr/local/bin/node" "/Users/zhang/Documents/data_insight_yinzhou/data_insight_yinzhou/node_modules/node-gyp/bin/node-gyp.js" "rebuild" "--verbose" "--libsass_ext=" "--libsass_cflags=" "--libsass_ldflags=" "--libsass_library="
gyp ERR! cwd /Users/zhang/Documents/data_insight_yinzhou/data_insight_yinzhou/node_modules/node-sass
gyp ERR! node -v v10.1.0
gyp ERR! node-gyp -v v3.6.2
gyp ERR! not ok 
```

查看资料发现，可能需要增加--unsafe-perm，即

``` sudo npm install node-sass --unsafe-perm --save-dev
sudo npm install node-sass --unsafe-perm --save-dev
```

或:

```
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

