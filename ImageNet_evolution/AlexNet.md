# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 1. 网络结构

<center><div id="diagram"></div></center>
<div><textarea id="code" style="width: 100%;" rows="11">
st=>start: Image (3 * 224 * 224)
conv1=>inputoutput: Conv [k: 11, s: 4, c: 96] + ReLU
norm1=>inputoutput: LRN
pool1=>inputoutput: Max Pooling [k: 3, s: 2]
pool1_output=>operation: (96 * 27 * 27)
conv2=>inputoutput: Conv [k: 5, s: 1, p: 2, c: 256] + ReLU
norm2=>inputoutput: LRN
pool2=>inputoutput: Max Pooling [k: 3, s: 2]
pool2_output=>operation: (256 * 13 *13)
conv3=>inputoutput: Conv [k: 3, s: 1, p: 1, c: 384] + ReLU
conv4=>inputoutput: Conv [k: 3, s: 1, p: 1, c: 384] + ReLU
conv5=>inputoutput: Conv [k: 3, s: 1, p: 1, c: 256] + ReLU
pool5=>inputoutput: Max Pooling [k: 3, s: 2]
pool5_output=>operation: (256, 6, 6)
fc6=>inputoutput: fc [o: 4096]
fc7=>inputoutput: fc [o: 4096]
fc8=>inputoutput: fc [o: 1000]
e=>end: prediction output

st->conv1->norm1
norm1->pool1->pool1_output
pool1_output->conv2->norm2
norm2->pool2->pool2_output
pool2_output->conv3->conv4->conv5->pool5->pool5_output
pool5_output->fc6->fc7->fc8->e
</textarea></div>
<script>
    document.getElementById("code").style.display = "none";
    var cd = document.getElementById("code");
    var code = cd.value;
    var diagram = flowchart.parse(code);
    diagram.drawSVG('diagram');
</script>