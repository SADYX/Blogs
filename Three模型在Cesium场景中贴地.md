所谓Three和Cesium融合，即canvas叠加，将Three的canvas置顶，通过Cesium camera transform matrix来控制Three camera，达到“融合”的效果。
如果要使Three中的模型在Cesium中达到贴地的视觉效果，则需要旋转坐标系。（Cesium中的坐标轴是Y向上，Three是Z向上）
首先需要用Cesium.Transforms.eastNorthUpToFixedFrame计算出世界坐标系到贴地的目标坐标系的变换矩阵Matrix4。
取出Matrix4的平移部分，赋给Three model position。
接着取出旋转的Matrix3，但这个是不能直接给Three model的，因为正如上面所说，两个框架的坐标系的不同的，所以需要翻转一下。
先绕X轴旋转-90°，再绕Z轴旋转+90°

```javascript
const deg2rad = (deg: number) => (deg * Math.PI) / 180;

const getThreeModelQuaternion = (
  longitude: number,
  latitude: number,
  degree: number = 0,
) => {
  const m4 = Cesium.Transforms.eastNorthUpToFixedFrame(
    Cesium.Cartesian3.fromDegrees(longitude, latitude),
  );
  const m3 = Cesium.Matrix4.getMatrix3(m4, new Cesium.Matrix3());
  const q = Cesium.Quaternion.fromRotationMatrix(m3, new Cesium.Quaternion());
  const qx = Cesium.Quaternion.fromRotationMatrix(
    new Cesium.Matrix3(1, 0, 0, 0, 0, -1, 0, 1, 0),
    new Cesium.Quaternion(),
  );
  const qz = Cesium.Quaternion.fromRotationMatrix(
    new Cesium.Matrix3(0, 1, 0, -1, 0, 0, 0, 0, 1),
    new Cesium.Quaternion(),
  );
  const angle_r = deg2rad(degree);
  const qy = Cesium.Quaternion.fromRotationMatrix(
    new Cesium.Matrix3(
      Math.cos(angle_r),
      0,
      -Math.sin(angle_r),
      0,
      1,
      0,
      Math.sin(angle_r),
      0,
      Math.cos(angle_r),
    ),
    new Cesium.Quaternion(),
  );
  const _q1 = Cesium.Quaternion.multiply(q, qz, new Cesium.Quaternion());
  const _q2 = Cesium.Quaternion.multiply(_q1, qx, new Cesium.Quaternion());
  const _q3 = Cesium.Quaternion.multiply(_q2, qy, new Cesium.Quaternion());

  return [_q3.x, _q3.y, _q3.z, _q3.w];
};
```

当时写的时候不知道为什么要把矩阵转成四元数再乘，实际上直接矩阵相乘就行了。
