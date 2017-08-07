# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 1. 网络结构

- image (3 \* 224 \* 224) --> [k: 11 \* 11 \* 96, s: 4 \* 4 conv] --> ReLU --> LRN --> (96 \* 55 \* 55) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (96 \* 27 \* 27) --> [k: 5 \* 5 \* 256, s: 1 \* 1, p: 2 conv] --> ReLU --> LRN --> (256 \* 27 \* 27) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 13 \* 13) --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 256, s: 1 \* 1, p: 1 conv] --> ReLU --> (256 \* 13 \* 13) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 6 \* 6) --> [4096 fc] --> (4096) --> [4096 fc] --> (4096) --> [1000 fc]--> (1000)

<div id="diagram">Diagram will be placed here</div>
<script>
    var diagram = flowchart.parse('st=>start: Start:>http://www.google.com[blank]\n' +
                                'e=>end:>http://www.google.com\n' +
                                'op1=>operation: My Operation\n' +
                                'sub1=>subroutine: My Subroutine\n' +
                                'cond=>condition: Yes or No?\n:>http://www.google.com' +
                                'io=>inputoutput|request: catch something...\n' +
                                '' +
                                'st->op1->cond\n' +
                                'cond(yes)->io->e\n' + // conditions can also be redirected like cond(yes, bottom) or cond(yes, right)
                                'cond(no)->sub1(right)->op1');
    diagram.drawSVG('diagram');
</script>