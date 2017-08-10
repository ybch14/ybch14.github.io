# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 网络结构

<center><div id="diagram"></div></center>
<div>
<textarea id="flow-chart-code">
st=>start: Image
st_blob=>operation: 3 * 224 * 224
conv1=>inputoutput: Conv [k = 11, s = 4, c = 96] + ReLU + LRN
96 * 54 * 54
pool1=>inputoutput: Max Pooling [k = 3, s = 2]
96 * 27 * 27
conv2=>inputoutput: Conv [k = 5, s = 1, p = 2, c = 256] + ReLU + LRN
256 * 27 * 27
pool2=>inputoutput: Max Pooling [k = 3, s = 2]
256 * 13 * 13
conv3=>inputoutput: Conv [k = 3, s = 1, p = 1, c = 384] + ReLU
384 * 13 * 13
conv4=>inputoutput: Conv [k = 3, s = 1, p = 1, c = 384] + ReLU
384 * 13 * 13
conv5=>inputoutput: Conv [k = 3, s = 1, p = 1, c = 256] + ReLU
384 * 13 * 13
pool5=>inputoutput: Max Pooling [k = 3, s = 2]
256 * 6 * 6
fc6=>inputoutput: fc [o: 4096]
4096
fc7=>inputoutput: fc [o: 4096]
4096
fc8=>inputoutput: fc [o: 1000]
4096
e=>end: prediction output

st->conv1->pool1->conv2->pool2->conv3->conv4->conv5->pool5->fc6->fc7->fc8->e
st->st_blob
</textarea>
</div>
<script>
    document.getElementById("flow-chart-code").style.display = "none";
    var cd = document.getElementById("flow-chart-code");
    var code = cd.value;
    var diagram = flowchart.parse(code);
    diagram.drawSVG('diagram', {'text-align': 'center'});
</script>