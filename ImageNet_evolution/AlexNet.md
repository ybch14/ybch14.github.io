# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 1. 网络结构

- image (3 \* 224 \* 224) --> [k: 11 \* 11 \* 96, s: 4 \* 4 conv] --> ReLU --> LRN --> (96 \* 55 \* 55) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (96 \* 27 \* 27) --> [k: 5 \* 5 \* 256, s: 1 \* 1, p: 2 conv] --> ReLU --> LRN --> (256 \* 27 \* 27) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 13 \* 13) --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 384, s: 1 \* 1, p: 1 conv] --> ReLU --> [k: 3 \* 3 \* 256, s: 1 \* 1, p: 1 conv] --> ReLU --> (256 \* 13 \* 13) --> [k: 3 \* 3, s: 2 \* 2 max-pooling] --> (256 \* 6 \* 6) --> [4096 fc] --> (4096) --> [4096 fc] --> (4096) --> [1000 fc]--> (1000)

<div id="diagram"></div>
<div><textarea id="code" style="width: 100%;" rows="11">
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
</textarea></div>