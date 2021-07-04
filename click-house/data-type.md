# 数据类型
## 比较
MySQL|ClickHouse
:-:|:-:
byte|Int8
short|Int16
int|Int32
long|Int64
varchar|String
timestamp|DateTime
float|Float32
double|Float64
boolean|--

## 注意点
- 严格区分大小写
- 上面表格只是列了一部分
- 无符号的Int8[-2<sup>7</sup>, 2<sup>7</sup>-1]对应UInt8[0, 2<sup>8</sup>-1]
- FixString(N) 限制字符串最大长度
- 尽量避免浮点数，存在精度损失问题
- 与标准SQL相比，ClickHouse支持以下浮点数
  - Inf 正无穷
    - 1÷0 是不会报错，返回正无穷
  - -Inf 负无穷
  - NaN 非数字
    - 0÷0
- 不支持boolean，使用枚举enum代替
  - Enum8、Enum16
- 数组，任意类型，但是只能是相同类型
- 元组，补充了数组不能存储多个数据类型的不足