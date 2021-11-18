# json comparison

去年年底开始，组里开始做了一次重构。为了保证质量、增加信心、证明成果，必须要做对比测试。

对比测试的方法很直接，找两群机器，接同样的数据源，找一些(过往的)真实流量同时往新旧系统上打，比较结果即可。我们长年保有pulog，真实流量是现成的。来的请求基本都走odata，所以拿的是json，给的也是json。

已经有一个对比工具了，但它一次只能跑一种请求，比如Get{AggMcc,}{Summary,Notification}，它们在各业务各有扩展类，需分别跑。GET/POST也得分别跑。它还需要指定req/resp的类型，于是每个业务组也要分别改代码再跑自己的对比测试。

我觉得这很愚蠢。pulog里有method，有url，有header，有payload，完全可以重现请求。而json是有语义的，浮点数可以舍入到指定精度，需要忽略的项可以用jsonpath识别，因而不用依赖schema.dll。

尝试几天后发现，愚蠢的是我自己。summary是个array；算数据的时候是并行，先到先填，全填完再排序，但排序key有可能是一样的，所以最终结果的顺序是不固定。而notification的负载竟然不是json，而是其序列化后的字符串。不得不拜服这些赏口饭吃的前辈。

**教训**
- 这个世界上的事情啊真就不能脑补
- 要尊重shishan

但最后还是用了自己的工具。在我看来，重做请求、收发、比较、生成报告，是边界非常清晰的顺序步骤，只有比较这一步会略有不同。切不干净，忍不了。引入比较器，给出OperationName到切出feature方法的映射，世界清净了。

## P.S.
尝试了一些(免费的)文件比较工具，有些不满
- 跳转是「行」级别的。如果一行特别长，无法跳到差异字符
- 无法「收起」json array/object
- 打开100M的文件会卡死
似乎可以做一做？
