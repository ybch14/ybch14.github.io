# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 网络结构

<script src="https://cdnjs.cloudflare.com/ajax/libs/mermaid/7.0.0/mermaid.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
<center><div class="mermaid">
    graph TD;
    image["Image"];
    conv1["Conv [k = 11, s = 4, c = 96] + ReLU + LRN"];
    pool1["Max Pooling [k = 3, s = 2]"];
    conv2["Conv [k = 5, s = 1, p = 2, c = 256] + ReLU + LRN"];
    pool2["Max Pooling [k = 3, s = 2]"];
    conv3["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"];
    conv4["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"];
    conv5["Conv [k = 3, s = 1, p = 1, c = 256] + ReLU"];
    pool5["Max Pooling [k = 3, s = 2]"];
    fc6["fc [o = 4096]"];
    fc7["fc [o = 4096]"];
    fc8["fc [o = 1000]"];
    pred["ImageNet classification result"];
    image -->|"3 * 224 * 224"| conv1;
    conv1 -->|"96 * 54 * 54"| pool1;
    pool1 -->|"96 * 27 * 27"| conv2;
    conv2 -->|"256 * 27 * 27"| pool2;
    pool2 -->|"256 * 13 * 13"| conv3;
    conv3 -->|"384 * 13 * 13"| conv4;
    conv4 -->|"384 * 13 * 13"| conv5;
    conv5 -->|"256 * 13 * 13"| pool5;
    pool5 -->|"256 * 6 * 6"| fc6;
    fc6 -->|"4096"| fc7;
    fc7 -->|"4096"| fc8;
    fc8 -->|"1000"| pred;
</div></center>
