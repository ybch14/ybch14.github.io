# AlexNet

第一个用于 ImageNet 分类的深度网络。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 1. 网络结构

<div id="diagram"></div>
<div><textarea id="code" style="width: 100%;" rows="11">
<!-- st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request -->

<!-- st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e -->
st=>start: Image (3 * 224 * 224)
conv1=>inputoutput: k 11 * 11 * 96<br />s 4 * 4
e=>end: cls

st->conv1->e
</textarea></div>
<script>
    document.getElementById("code").style.display = "none";
    var cd = document.getElementById("code");
    var code = cd.value;
    var diagram = flowchart.parse(code);
    diagram.drawSVG('diagram');
</script>