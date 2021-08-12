2021/6/18<br>
最近开发遇到了一个很奇怪的bug。3dtile.style = ... ; 和 viewer.scene.pickDrill 放在一起居然会使页面卡死，也没有报错。最终换成viewer.scene.pick才解决问题。


2021/8/12<br>
entity的边框和shaderlibrary里的边框同时用会报错 => [link here](https://github.com/CesiumGS/cesium/issues/9698)
