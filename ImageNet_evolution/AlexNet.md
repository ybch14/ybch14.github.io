# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 网络结构

<!-- <center><div id="diagram"></div></center>
<div>
<textarea id="flow-chart-code">
st=>start: Image 3 * 224 * 224
conv1=>condition: Conv [k = 11, s = 4, c = 96] + ReLU + LRN
conv_blob=>operation: 96 * 54 * 54
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

st->conv1(yes)->pool1->conv2->pool2->conv3->conv4->conv5->pool5->fc6->fc7->fc8->e
</textarea>
</div>
<script>
    document.getElementById("flow-chart-code").style.display = "none";
    var cd = document.getElementById("flow-chart-code");
    var code = cd.value;
    var diagram = flowchart.parse(code);
    diagram.drawSVG('diagram', {'text-align': 'center'});
</script> -->

<script src="https://cdnjs.cloudflare.com/ajax/libs/mermaid/7.0.0/mermaid.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
<center><div class="mermaid">
    graph TD;
    image["Image"] --- image_blob["3 * 224 * 224"];
    conv1["Conv [k = 11, s = 4, c = 96] + ReLU + LRN"] --- conv1_blob["96 * 54 * 54"];
    pool1["Max Pooling [k = 3, s = 2]"] --- pool1_blob["96 * 27 * 27"];
    conv2["Conv [k = 5, s = 1, p = 2, c = 256] + ReLU + LRN"] --- conv2_blob["256 * 27 * 27"];
    pool2["Max Pooling [k = 3, s = 2]"] --- pool2_blob["256 * 13 * 13"];
    conv3["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"] --- conv3_blob["384 * 13 * 13"];
    conv4["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"] --- conv4_blob["384 * 13 * 13"];
    conv5["Conv [k = 3, s = 1, p = 1, c = 256] + ReLU"] --- conv5_blob["256 * 13 * 13"];
    pool5["Max Pooling [k = 3, s = 2]"] --- pool5_blob["256 * 6 * 6"];
    fc6["fc [o = 4096]"] --- fc6_blob["4096"];
    fc7["fc [o = 4096]"] --- fc7_blob["4096"];
    fc8["fc [o = 1000]"] --- fc8_blob["1000"];
    image --> conv1;
    conv1 --> pool1;
    pool1 --> conv2;
    conv2 --> pool2;
    pool2 --> conv3;
    conv3 --> conv4;
    conv4 --> conv5;
    conv5 --> pool5;
    pool5 --> fc6;
    fc6 --> fc7;
    fc7 --> fc8;
</div></center>
