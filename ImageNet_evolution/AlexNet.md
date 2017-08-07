# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 1. 网络结构

- image (3 \* 224 \* 224) --> [k: 11 \* 11 \* 96, s: 4 \* 4 conv] --> ReLU --> LRN --> (96 \* 55 \* 55) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (96 \* 27 \* 27) --> [k: 5 \* 5 \* 256, s: 1 \* 1, p: 2 conv] --> ReLU --> LRN --> (256 \* 27 \* 27) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 13 \* 13) --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 256, s: 1 \* 1, p: 1 conv] --> ReLU --> (256 \* 13 \* 13) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 6 \* 6) --> [4096 fc] --> (4096) --> [4096 fc] --> (4096) --> [1000 fc]--> (1000)

<script>
var diagram = flowchart.parse('st=>start: Start\ne=>end\nop1=>operation: My Operation\n');
diagram.drawSVG('diagram', {'line-width': 3, 'line-length': 50, 'text-margin': 10, 'font-size': 14, 'font-color': 'black', 'line-color': 'black', 'element-color': 'black', 'fill': 'white', 'yes-text': 'yes', 'no-text': 'no', 'arrow-end': 'block', 'scale': 1});
</script>