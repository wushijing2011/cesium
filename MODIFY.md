### ScreenSpaceCameraController.js

* 修改默认值

```
    this._zoomFactor = 1; // 调慢缩放速度，原始默认值 5
    this._maximumRotateRate = 0.5; // 调慢旋转速度，原始默认值 1.77
```

* fix bug ： 缩放抖动

  + 变量 movement 鼠标缩放距离， movement.startPosition 恒为 `{ x: 0, y: 0}`, movement.endPosition.x 恒为 0， 只有 movement.endPosition.y 代表鼠标缩放距离， 为正值时鼠标动作为‘放大’，为负值时鼠标动作为’缩小‘。
  + 变量 beta 为经过一系列计算得到的某个夹角 beta < 0 时 动作为‘缩小’，反之为’放大‘。
  + 以下代码作用是：忽略鼠标动作是’放大‘但计算出的 beta 值为’缩小‘的情况。
```
    if(beta < 0){
        if (movement.endPosition.y >= 0){ // fix camera jump in and out
            return;
        }
    }
```

<!-- * 俯仰角调整方向翻转

```
    // var deltaTheta = rotateRate * thetaWindowRatio * Math.PI;
    // 俯仰角调整方向翻转
    var deltaTheta = -rotateRate * thetaWindowRatio * Math.PI;
``` -->

* fix bug: 调用 camera.lookAtTransform() enableTilt 失效

```
    if (!Matrix4.equals(camera.transform, Matrix4.IDENTITY)) {
        // rotate3D(controller, startPosition, movement);
        rotate3D(controller, startPosition, movement, null, false, !controller.enableTilt);
        return;
    }
```


* feature: 出错后禁用弹出窗口并继续render
```
    // widget._useDefaultRenderLoop = false;
    // widget._renderLoopRunning = false;
    // if (widget._showRenderLoopErrors) {
    //     var title = 'An error occurred while rendering.  Rendering has stopped.';
    //     widget.showErrorPanel(title, undefined, error);
    // }
    if (isNaN(widget.camera.position.x)) {
        widget.camera.position = Cartesian3.fromDegrees(0, 0, 9000000);
        widget.camera.setView();
    } else {
        console.info('render error:', error, widget);
    }
    requestAnimationFrame(render);
```
