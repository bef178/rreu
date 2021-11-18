# getopt brief

有一个问题：如何从命令行(优雅地)解析出参数。

“有问题就会有答案”。

很多软件/库尝试给出答案，其中最著名的是getopt和getopts。

**getopt**在1980年前就出现了，它的输出实际上是重新排列后的参数列表。早期版本的主要缺点在于，“could not handle whitespace or shell metacharacters in arguments, and there was no ability to disable the output of error messages”。

``` bash
scriptname=$0
normalized=$(getopt -o ab:c:: --long a-long,b-long:,c-long:: -n "$scriptname" -- "$@")
errno=$?
if [ $errno != 0 ]; then
    echo "E: getopt returns $errno" >&2
    exit $errno;
fi

eval set -- "$normalized"

while true; do
    case "$1" in
        -a|--a-long)
            echo "I: opt: $1"
            shift
            ;;
        -b|--b-long)
            echo "I: opt: $1, value: $2"
            shift 2
            ;;
        -c|--c-long)
            # value can be empty
            echo "I: opt: $1, value: $2"
            shift 2
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "E: unknown opt: $1"
            ;;
    esac
done

for arg; do
    echo "I: non-opt-arg: $arg"
done
```

**getopts**始于1986，内置在sh/bash里，意图替代getopt。它依赖预设变量OPTIND和OPTARG来维持状态和传递参数；遇到非选项参数会立即中止解析；必须通过某些“奇妙”的方式才能支持长选项。

``` bash
while getopts ":ab:c::-:" opt
do
    case $opt in
        -)
            case $OPTARG in
                b-long)
                    opt="$OPTARG"
                    value="${!OPTIND}"
                    OPTIND=$(( $OPTIND + 1 ))
                    echo "I: opt: $opt, value: $value"
                    ;;
                b-long=*)
                    opt=${OPTARG%=$value}
                    value=${OPTARG#*=}
                    echo "I: opt: $opt, value: $value"
                    ;;
                *)
                    echo "E: unknown option: $OPTARG"
                    ;;
            esac
            ;;
        a)
            value="true"
            echo "I: opt: $opt, value: $value"
            ;;
        b)
            value=$OPTARG
            echo "I: opt: $opt, value: $value"
            ;;
        *)
            echo "E: unknown opt: $OPTARG"
            ;;
    esac
done
shift $(($OPTIND - 1))

for arg; do
    echo "I: non-opt-arg: $arg"
done
```

这两者各自进化。今天看来，“意图”已经变成了“企图”。在现代的Linux里，似乎getopt更好用一些。

## 前端之魂？

想给出另一个答案吗？必须要搞搞清楚需求。
- 短选项以“-”开始，只有一个字母或数字
- 长选项以“--”开始，字符集是字母、数字以及“-”
- 支持{长,短}选项
- 支持连写短选项
- 支持选项{无,有,可选}参数，可选参数必须通过连写才能认出

多少得有点不一样吧？

- 把双冒号改改？
- 长短选项可以统一地给在一个字符串里吗？短选项的连写会跪吗？也许，可以允许相同前缀的、不带参数的短选项连写？
- 选项前缀“-”和“--”是写死的吗？
- “-3”是非选项参数负三还是未定义的名为三的参数？
- 输出可以是个array of key value？

```bash
optstr="-a,--a-long,+b:,-b:,--b-long:,-c^,c-long^"

# somescript -accarg -3 +b barg --b-long blarg --a-long=alarg c-long xxx
# => ["-a":true,"-c":"carg","!opt":"-3","+b":"barg", "--b-long":"blarg","--a-long":"alarg","c-long":"","!opt":"xxx"]
```

那么，值得动手吗？
