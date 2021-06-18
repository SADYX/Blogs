2021.6.18

最近开发遇到了一个很奇怪的bug。3dtile.style = ... ; 和 viewer.scene.pickDrill 放在一起居然会使页面卡死，也没有报错。最终换成viewer.scene.pick才解决问题。