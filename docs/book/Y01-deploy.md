# 源码管理

## main
* 正式环境部署分支
* 每次合并触发打包
* 每次打包手动打TAG

## release、UAT ？？？？
* 预生产环境

## develop
* 测试环境部署分支
* 每次合并触发打包

## develop-x.x.x
* 开发功能分支
* 测试通过后才能合并到develop

## fix
* 线上出了问题，每次拉去main分支，进行修改