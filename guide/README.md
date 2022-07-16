---
sidebar: auto
pageClass: custom-page-class
---

# 前言
最近想深入学习一下solidity，所以找了很多教程，发现 [Solidity by Example | 0.8.13](https://solidity-by-example.org/) 对于入门很好，但是只有英文版。中文版本 [GitHub - AmazingAng/WTFSolidity](https://github.com/AmazingAng/WTFSolidity) 写的非常详细，但是对于入门的选手有的章节有点难度，所以我就想结合两个一起做一下笔记，能够同时合并一下案例，将这些学习笔记一起开放出来。

# 学习小结
## 原始数据类型
在这里，我们向您介绍 Solidity 中可用的一些原始数据类型。
-   `boolean`
-   `uint`
-   `int`
-   `address`

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Primitives {

    bool public boo = true;

/*
    uint 代表无符号整数，意思是非负整数
    有不同的尺寸可供选择
    uint8 范围从 0 到 2 ** 8 - 1
    uint16 范围从 0 到 2 ** 16 - 1
    ...
    uint256 范围从 0 到 2 ** 256 - 1

    带了public的是可以访问到的
    变量默认的值是：internal，访问不到
*/

    uint8 public u8 = 1;
    uint public u256 = 456;
    uint public u =123;

/*
    int 类型允许使用负数。
    与 uint 一样，从 int8 到 int256 都有不同的范围可用
    int256 范围从 -2 ** 255 到 2 ** 255 - 1
    int128 范围从 -2 ** 127 到 2 ** 127 - 1
*/

    int8 public i8 = -1;
    int public i256 = 456;
    int public i = -123; // int is same as int256

// minimum and maximum of int
    int public minInt = type(int).min;
    int public maxInt = type(int).max;

    address public addr = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;


/*
    在 Solidity 中，数据类型 byte 表示一个字节序列。
    Solidity 提供了两种字节类型：
    - 固定大小的字节数组
    - 动态大小的字节数组。
    Solidity 中的术语字节表示一个动态的字节数组。
    它是 byte[] 的简写。
*/
    // bytes1 没有定义 public，默认是internal，在编译之后不会出现
    bytes1 a = 0xb5; //  [10110101]
    bytes1 b = 0x56; //  [01010110]

//  如果是string类型的，使用bytes

    bytes public str = "this is test";

//  默认值
//  未赋值的变量有一个默认值
    bool public defaultBoo; // false
    uint public defaultUint; // 0
    int public defaultInt; // 0
    address public defaultAddr; // 0x0000000000000000000000000000000000000000

}
```



## 变量
Solidity 中有 3 种类型的变量
local
	在函数内部声明
	不存储在区块链上
state
	在函数外声明
	存储在区块链上
global（提供有关区块链的信息）
	例如msg.sender

```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Variables {
    // 状态变量存储在区块链上。
    string public text = "Hello";
    uint public num = 123;

    function doSomething() public {
        // 局部变量不会保存到区块链中。
        uint i = 456;

        // 下面是一些全局变量
        uint timestamp = block.timestamp; // 当前区块时间戳
        address sender = msg.sender; // 发送者的地址
    }
}

```











## 常量
常量是不能被修改的变量。
它们的值是硬编码的，使用常数可以节省gas成本。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Constants {
    // 编码惯例为大写的常量变量
    // 类似js里面的const
    address public constant MY_ADDRESS = 0x777788889999AaAAbBbbCcccddDdeeeEfFFfCcCc;
    uint public constant MY_UINT = 123;
}

```

## 不可变变量
不可变变量就像常量。不可变变量的值可以在构造函数中设置，但之后不能修改。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Immutable {
    // coding convention to uppercase constant variables
    address public immutable MY_ADDRESS;
    uint public immutable MY_UINT;

    constructor(uint _myUint) {
        MY_ADDRESS = msg.sender;
        MY_UINT = _myUint;
    }
}

```

## 练习：读取和写入状态变量
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract SimpleStorage {
	// 定义一个变量num
    uint public num;
	// 发送一个交易，设置一个变量，需要花费gas
    function set(uint _num) public {
        num = _num;
    }
	// 获得变量num，不过上述是num，应该就自动生成了一个获取了
    function get() public view returns(uint){
        return num;
    }
}
```

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220619165406.png)

## Ether 和 Wei
交易是用ether支付的。
1ether等于 10^18wei
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract EtherUnits {
    uint public oneWei = 1 wei;
    // 1 wei is equal to 1
    bool public isOneWei = 1 wei == 1;

    uint public oneEther = 1 ether;
    // 1 ether is equal to 10^18 wei
    bool public isOneEther = 1 ether == 1e18;
}

```
运行过后的值
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220619170548.png)



## Gas
*你需要为一笔交易支付多少gas？*
你要支付花费的gas = gas spend * gas price，其中ether一个计算单位
gas消耗是交易中使用的gas总量
gas price是你愿意为每个gas支付多少ether的单价
gas price较高的交易有较高的优先权被纳入一个区块中。
未消耗的gas将被退还。
*Gas limit*
你可以花费的gas量有两个上限
* gas limint（你愿意为你的交易使用的gas，由你设定）
* block gas limit（一个区块中允许的最大gas，由网络设置）
```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Gas {
    uint public i = 0;

    // Using up all of the gas that you send causes your transaction to fail.
    // State changes are undone.
    // Gas spent are not refunded.
    function forever() public {
        // Here we run a loop until all of the gas are spent
        // and the transaction fails
        while (true) {
            i += 1;
        }
    }
}

```

## If/Else
支持if/else 也支持 三元运算符

函数后面 pure/view 表示不需要花费gas

pure：不能读取也不能写入存储链上的数据
view：能读取状态，但是不能写入

```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract IfElse {
    function foo(uint x) public pure returns(uint){
        if(x < 10){
            return 0;
        } else if (x <20) {
            return 1;
        } else {
            return 2;
        }
    }

    function ternary(uint _x) public pure returns(uint) {
        return _x < 10 ? 1: 2;
    }
}
```


## For 和 While Loop

Solidity 支持 for、while 和 do while 循环。
不要编写无界循环，因为这可能会达到 gas 限制，从而导致您的交易失败。
由于上述原因，很少使用 while 和 do while 循环。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Loop {
    function loop() public {
        // for loop
        for (uint i = 0; i < 10; i++) {
            if (i == 3) {
                // Skip to next iteration with continue
                continue;
            }
            if (i == 5) {
                // Exit loop with break
                break;
            }
        }

        // while loop
        uint j;
        while (j < 10) {
            j++;
        }
    }
}

```














## Mapping 映射
映射的创建语法是mapping(keyType => valueType)。
keyType可以是任何内置的值类型，字节，字符串，或任何合约。
valueType可以是任何类型，包括另一个映射或一个数组。
映射是不可迭代的。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Mapping {
    mapping(address => uint) public myMap;
    // 得到映射
    function get(address _addr) public view returns(uint){
        return myMap[_addr];
    }

    // 更新映射
    function set(address _addr, uint _i) public {
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        delete myMap[_addr];
    }
}
```

先设置myMap的值，然后再用get获取

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621153740.png)

### 嵌套映射

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract NestedMapping {
    mapping(address => mapping(uint => bool)) public nested;

    function get(address _addr1, uint _i) public view returns (bool){
        return nested[_addr1][_i];
    }

    function set(address _addr1, uint _i, bool _boo) public {
        nested[_addr1][_i] = _boo;
    }
}
```

没有初始化的时候，默认是false

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621154902.png)

设置过后，值发生了变化

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621155151.png)

直接调用 `nested` 的方法和get是一样的，因为nested是个public，public变量会自动生成getter函数用户查询 [WTFSolidity/03_Function at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/03_Function#solidity%E4%B8%AD%E7%9A%84%E5%87%BD%E6%95%B0)

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621155221.png)

## Array 数组

数组可以有一个编译时的固定大小，也可以有一个动态大小。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Array {
    // 几种初始化数组的方法,不定长数组
    uint[] public arr;
    uint[] public arr2 = [1,2,3];
    // 定长数组，所有初始化值为0
    uint[] public myFixedSizeArr;

    function get(uint i) public view returns(uint) {
        return arr[i];
    }

    function getArr() public view returns (uint[] memory){
        //solidity 可以返回整个数组，但是需要避免返回无限增长的数组
        // 数组放回的时候，必须是 "memory" 或者 "calldata
        return arr;
    }


    function push(uint i) public {
        // 给数组添加元素
        // 长度会增加1
        arr.push(i);
    }

    function pop() public {
        // 移除数组最后一个元素
        // 长度会-1
        arr.pop();
    }

    function getLength() public view returns (uint) {
        return arr.length;
    }

    function remove(uint index) public {
        // 删除不会更改数组长度。
        // 它将索引处的值重置为它的默认值。
        // 这里会设置为0
        delete arr[index];
    }


     //定义address类型的数组
    address[] public addressArr;
    bytes public bytesArr;

    function setAddress() public returns (address[] memory){
        addressArr.push(msg.sender);
        return addressArr;
    }


    function examples() external pure returns (uint[] memory)  {
        // 在内存中创建数组，只能创建固定大小的数组
        uint[] memory a = new uint[](5);
        return a;
    }

}
```

### 测试push 和 pop

push一个数字之后，再调用arr，就会输出刚才push的值

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621161845.png)

### 测试address数组

```c
     //定义address类型的数组
    address[] public addressArr;
    bytes public bytesArr;

    function setAddress() public returns (address[] memory){
        addressArr.push(msg.sender);
        return addressArr;
    }
```

输出如下，需要先调用 `setAddress` 后续调用数组下标为0，输出就是当前的合约地址

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621161635.png)

### 测试example

```c
function examples() external pure returns (uint[] memory)  {
        // 在内存中创建数组，只能创建固定大小的数组
        // 并且返回该数组，默认都为0
        uint[] memory a = new uint[](5);
        return a;
    }
```

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621161956.png)

### 移除数组元素的例子
通过从右向左移动元素来删除数组元素
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ArrayRemoveByShifting {
    // [1, 2, 3] -- remove(1) --> [1, 3, 3] --> [1, 3]
    // [1, 2, 3, 4, 5, 6] -- remove(2) --> [1, 2, 4, 5, 6, 6] --> [1, 2, 4, 5, 6]
    // [1, 2, 3, 4, 5, 6] -- remove(0) --> [2, 3, 4, 5, 6, 6] --> [2, 3, 4, 5, 6]
    // [1] -- remove(0) --> [1] --> []

    uint[] public arr;

    function remove(uint _index) public {
        require(_index < arr.length, "index out of bound");

        for (uint i = _index; i < arr.length - 1; i++) {
            arr[i] = arr[i + 1];
        }
        arr.pop();
    }

    function test() external {
        arr = [1, 2, 3, 4, 5];
        remove(2);
        // [1, 2, 4, 5]
        assert(arr[0] == 1);
        assert(arr[1] == 2);
        assert(arr[2] == 4);
        assert(arr[3] == 5);
        assert(arr.length == 4);

        arr = [1];
        remove(0);
        // []
        assert(arr.length == 0);
    }
}

```

通过将最后一个元素复制到要删除的位置来删除数组元素

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ArrayReplaceFromEnd {
    uint[] public arr;

    // Deleting an element creates a gap in the array.
    // One trick to keep the array compact is to
    // move the last element into the place to delete.
    function remove(uint index) public {
        // Move the last element into the place to delete
        arr[index] = arr[arr.length - 1];
        // Remove the last element
        arr.pop();
    }

    function test() public {
        arr = [1, 2, 3, 4];

        remove(1);
        // [1, 4, 3]
        assert(arr.length == 3);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
        assert(arr[2] == 3);

        remove(2);
        // [1, 4]
        assert(arr.length == 2);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
    }
}

```

## Enum 
Solidity 支持枚举，它们对于建模选择和跟踪状态很有用。
枚举可以在合约之外声明。
[WTFSolidity/02_ValueTypes at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/02_ValueTypes#5-%E6%9E%9A%E4%B8%BE-enum)
枚举（`enum`）是`solidity`中用户定义的数据类型。它主要用于为`uint`分配名称，是程序易于阅读和维护。它与`C语言`中的`enum`类似，把名称从`0`开始`uint`表示：
```c
   // 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
   enum ActionSet { Buy, Hold, Sell }
   // 创建enum变量 action
   ActionSet action = ActionSet.Buy;
```
它可以显式的和`uint`相互转换，并会检查转换的正整数是否在枚举的长度内，不然会报错：
```c
   // enum可以和uint显式的转换
   function enumToUint() external view returns(uint){
    return uint(action);
   }

```


```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Enum {
    // 声明一个枚举类型
    enum Status {
        Pending,
        Shipped,
        Accepted,
        Rejected,
        Canceled
    }

    // 默认值是列出的第一个元素
	// 类型的定义，在本例中为“Pending”
    Status public status;

    // Returns uint
    // Pending  - 0
    // Shipped  - 1
    // Accepted - 2
    // Rejected - 3
    // Canceled - 4
    function get() public view returns (Status) {
        return status;
    }

    // 通过将 uint 传递给输入来更新状态
    function set(Status _status) public {
        status = _status;
    }

    // 你可以像这样更新到一个特定的枚举上
    function cancel() public {
        status = Status.Canceled;
    }

    // delete 将枚举重置为其第一个值，0
    function reset() public {
        delete status;
    }
}
```

设置更新枚举型

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220622220553.png)


## Structs 结构体
[WTFSolidity/05_ArrayAndStruct at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/05_ArrayAndStruct#%E7%BB%93%E6%9E%84%E4%BD%93-struct)
你可以通过创建一个结构来定义你自己的类型。
它们对于将相关数据组合在一起非常有用。
结构可以在合同之外声明，并在另一个合同中导入。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Todos {
    struct Todo {
        string text;
        bool completed;
    }

    // 定义一个Todo结构体数组
    Todo[] public todos;

    function create(string calldata _text) public {
        // 3种初始化结构的方法
		// - 像函数一样调用它
        todos.push(Todo(_text, false));

        //键值映射
        todos.push(Todo({text: _text, completed: false}));

        // 初始化一个空结构然后更新它
        Todo memory todo;
        todo.text = _text;
        // todo.completed 初始化为 false

        todos.push(todo);
    }

    //Solidity 自动为“todos”创建了一个getter，所以你实际上并不需要这个函数。
    function get(uint _index) public view returns (string memory text, bool completed) {
        Todo storage todo = todos[_index];
        return (todo.text, todo.completed);
    }

    // 更新内容
    function updateText(uint _index, string calldata _text) public {
        Todo storage todo = todos[_index];
        todo.text = _text;
    }

    // 更新完成状态
    function toggleCompleted(uint _index) public {
        Todo storage todo = todos[_index];
        todo.completed = !todo.completed;
    }
}

```

### 测试 create 和 get
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220622223235.png)

### 测试 toggleCompleted
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220622223422.png)


### 申明和导入结构体
声明结构的文件
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
// This is saved 'StructDeclaration.sol'

struct Todo {
    string text;
    bool completed;
}

```

导入上述结构的文件

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "./StructDeclaration.sol";

contract Todos {
    // An array of 'Todo' structs
    Todo[] public todos;
}

```

## 数据存储位置 - Storage, Memory and Calldata

### 格式 
```c
{array|struct|mapping} {storage|memory|calldata} {data name}

uint[] storage _arr;
```

### 解释
[WTFSolidity/04_DataStorage at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/04_DataStorage#solidity%E4%B8%AD%E7%9A%84%E5%BC%95%E7%94%A8%E7%B1%BB%E5%9E%8B)
**引用类型(Reference Type)**：包括数组（`array`），结构体（`struct`）和映射（`mapping`），这类变量占空间大，赋值时候直接传递地址（类似指针）。由于这类变量比较复杂，占用存储空间大，我们在使用时必须要声明数据存储的位置。

变量被声明为 storage、memory 或 calldata 以明确指定数据的位置。

storage - 变量是一个状态变量（存储在区块链上）
memory - 变量在内存中，并且在调用函数时存在
calldata - 包含函数参数的特殊数据位置

###  数据位置
solidity数据存储位置有三类：`storage`，`memory`和`calldata`。不同存储位置的`gas`成本不同。`storage`类型的数据存在链上，类似计算机的硬盘，消耗`gas`多；`memory`和`calldata`类型的临时存在内存里，消耗`gas`少。大致用法：

1.  `storage`：合约里的状态变量默认都是`storage`，存储在链上。
    
2.  `memory`：函数里的参数和临时变量一般用`memory`，存储在内存中，不上链。
    
3.  `calldata`：和`memory`类似，存储在内存中，不上链。与`memory`的不同点在于`calldata`变量不能修改（`immutable`），一般用于函数的参数。例子：

```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract DataLocations {
    uint[] public arr;
    mapping(uint => address) map;
    struct MyStruct {
        uint foo;
    }

    mapping(uint => MyStruct) myStruct;

    function f() public {
        _f(arr, map, myStruct[1]);
        // 从映射种获得一个结构体
        MyStruct storage myStruct = myStruct[1];
        // 创建一个内存中的结构体
        MyStruct memory myMemStruct = MyStruct(0);
    }

    function _f(
        uint[] storage _arr,
        mapping(uint => address) storage _map,
        MyStruct storage _mySturct
    ) internal {

    }

    function g(uint[] memory _arr) public view returns (uint[] memory, uint[] memory){
        // 返回一个内存中的数组
        uint[] memory arr1  = _arr;
        return (arr1,arr);
    }

    function h(uint[] calldata _arr) external pure returns(uint[] memory, uint[] memory){
        // calldata 只能用作传入参数
        // 返回多个参数
        uint[] memory arr2 = _arr;
        uint[] memory arr3 = _arr;
        return (arr2,arr3);
    }

}


```

### 不同类型相互赋值时的规则

[WTFSolidity/04_DataStorage at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/04_DataStorage#%E4%B8%8D%E5%90%8C%E7%B1%BB%E5%9E%8B%E7%9B%B8%E4%BA%92%E8%B5%8B%E5%80%BC%E6%97%B6%E7%9A%84%E8%A7%84%E5%88%99)

1.  `storage`（合约的状态变量）赋值给本地`storage`（函数里的）时候，会创建引用，改变新变量会影响原变量。
2.  `storage`赋值给`memory`，会创建独立的复本，修改其中一个不会影响另一个；反之亦然。
3. `memory`赋值给`memory`，会创建引用，改变新变量会影响原变量。
4. 其他情况，变量赋值给`storage`，会创建独立的复本，修改其中一个不会影响另一个。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract DataStorage {
    // The data location of x is storage.
    // This is the only place where the
    // data location can be omitted.
    uint[] x = [1,2,3];

    function fStorage() public{
        //声明一个storage的变量xStorage，指向x。修改xStorage也会影响x
        uint[] storage xStorage = x;
        xStorage[0] = 100;
    }

    function fMemory() public view{
        //声明一个Memory的变量xMemory，复制x。修改xMemory不会影响x
        uint[] memory xMemory = x;
        xMemory[0] = 100;
    }

    function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
        //参数为calldata数组，不能被修改
        // _x[0] = 0 //这样修改会报错
        return(_x);
    }
}

```

## Function 函数
参考：[# Solidity极简入门:3. 函数类型](https://github.com/AmazingAng/WTFSolidity/tree/main/03_Function)
有几种方法可以从函数返回输出。
公共函数不能接受某些数据类型作为输入或输出

### function的结构

```C
  function (<parameter types>) {public|private|internal|external} [pure|view|payable] [returns (<return types>)]
```

1.  `function`：声明函数时的固定用法，想写函数，就要以function关键字开头。
    
2.  `(<parameter types>)`：圆括号里写函数的参数，也就是要输入到函数的变量类型和名字。
    
3.  `{internal|external|public|private}`：函数可见性说明符，一共4种。没标明函数类型的，默认`internal`。
    
    -   `public`: 内部外部均可见。(也可用于修饰状态变量，public变量会自动生成 `getter`函数，用于查询数值).
    -   `private`: 只能从本合约内部访问，继承的合约也不能用（也可用于修饰状态变量）。
    -   `external`: 只能从合约外部访问（但是可以用`this.f()`来调用，`f`是函数名）
    -   `internal`: 只能从合约内部访问，继承的合约可以用（也可用于修饰状态变量）。
4.  `[pure|view|payable]`：决定函数权限/功能的关键字。`payable`（可支付的）很好理解，带着它的函数，运行的时候可以给合约转入`ETH`。`pure`和`view`的介绍见下一节。
    
5.  `[returns ()]`：函数返回的变量类型和名称。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Function {
    // 函数可以返回多个值。
    function returnMany()
        public
        pure
        returns (
            uint,
            bool,
            uint
        )
    {
        return (1, true, 2);
    }

    // 返回值可以命名。
    function named()
        public
        pure
        returns (
            uint x,
            bool b,
            uint y
        )
    {
        return (1, true, 2);
    }

    // 返回值可以分配给它们的名称
    // 在这种情况下，return 语句可以省略。
    function assigned()
        public
        pure
        returns (
            uint x,
            bool b,
            uint y
        )
    {
        x = 1;
        b = true;
        y = 2;
    }

    // 调用另一个时使用解构赋值
    // 返回多个值的函数。
    // 利用(uint i, bool b, uint j) 去解构赋值
    function destructuringAssignments()
        public
        pure
        returns (
            uint,
            bool,
            uint,
            uint,
            uint
        )
    {
        (uint i, bool b, uint j) = returnMany();

        // 解构的时候，值得可以省略
        (uint x, , uint y) = (4, 5, 6);

        return (i, b, j, x, y);
    }

    // 不能将map(映射)用于输入或输出

    // 数组可以输入
    function arrayInput(uint[] memory _arr) public {}

    // 数组可以输出
    uint[] public arr;

    function arrayOutput() public view returns (uint[] memory) {
        return arr;
    }
}

```

### 返回值可以命名

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623151428.png)



### 解构的使用

```c
// 解构的时候，值得可以省略

(uint x, , uint y) = (4, 5, 6);
```

所以x,y 为4，6

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623151538.png)


## 函数的类型 view 和 pure
[WTFSolidity/03_Function at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/03_Function#solidity%E4%B8%AD%E7%9A%84%E5%87%BD%E6%95%B0)
Getter 函数可以声明为 view 或 pure。
View 函数声明不会更改任何状态。
纯函数声明不会更改或读取任何状态变量。
即view可以访问contract里定义的数据，而pure不可以。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ViewAndPure {
    uint public x = 1;

    // 不修改状态的返回。
    // 可以读取public
    function addToX(uint y) public view returns (uint) {
        return x + y;
    }

    // 承诺不修改或读取状态。
    // pure 不能读取 public
    function add(uint i, uint j) public pure returns (uint) {
        return i + j;
    }
}

```

查看结果

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623160245.png)


## Error 异常
一个错误将撤消在事务中对状态所做的所有改变。
你可以通过调用require、revert或assert抛出一个错误。
### require
require用于在执行前验证输入和条件。当满足条件的时候，放行。
```c
// 当i大于10的时候正常运行
require(_i > 10, "Input must be greater than 10");
```
### revert
revert与require类似。
```c
/ 当要检查的条件很复杂时，Revert 很有用。
        // 这段代码和上面的例子完全一样
        if (_i <= 10) {
            revert("Input must be greater than 10");
        }
```
### assert
assert是用来检查那些不应该是错误的代码。断言失败可能意味着有一个错误。

使用自定义错误来保存。
[WTFSolidity/13_Errors at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/13_Errors#solidity%E6%9E%81%E7%AE%80%E5%85%A5%E9%97%A8-13-%E5%BC%82%E5%B8%B8)
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Error {
    function testRequire(uint _i) public pure {
        /*
        // Require 应该用于验证条件，例如：
        // - 输入
        // - 执行前的条件
        // - 调用其他函数的返回值
        */
        require(_i > 10, "Input must be greater than 10");
    }

    function testRevert(uint _i) public pure {
        // 当要检查的条件很复杂时，Revert 很有用。
        // 这段代码和上面的例子完全一样
        if (_i <= 10) {
            revert("Input must be greater than 10");
        }
    }

    uint public num;

    function testAssert() public view {
        // assert应该只用于测试内部错误，
        // 并检查不变量。
        // 这里我们assert num总是等于0
        // 因为不可能更新 num 的值
        assert(num == 0);
    }

    // 自定义错误
    error InsufficientBalance(uint balance, uint withdrawAmount);

    function testCustomError(uint _withdrawAmount) public view {
    // 获取该合约地址的余额
        uint bal = address(this).balance;
        if (bal < _withdrawAmount) {
            revert InsufficientBalance({balance: bal, withdrawAmount: _withdrawAmount});
        }
    }
}

```

### testRequire输出

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623161332.png)

### testCustomError
自定义 error 搭配 revert 使用

可以看到该合约余额为0，所以会抛出自定义错误

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623162549.png)

### 三种错误的gas比较
[WTFSolidity/13_Errors at main # 三种方法的gas比较 · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/13_Errors#%E4%B8%89%E7%A7%8D%E6%96%B9%E6%B3%95%E7%9A%84gas%E6%AF%94%E8%BE%83)



----

## 函数修饰符  Function Modifier

### 格式
```
function_name (<parameter types>) {public|private|internal|external} modifier {pure|view|payable} {returns (<return types>)}
```

修饰符是可以在函数调用之前和/或之后运行的代码。
修饰符可用于：
1.限制访问
2.验证输入
3.防范重入黑客
4.支持多重继承

```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionModifier {
    // 我们将使用这些变量来演示如何使用 修饰符
    address public owner;
    uint public x = 10;
    bool public locked;

    constructor() {
        // 将交易发送者设置为合约的所有者。
        owner = msg.sender;
    }

    // 修饰符检查调用者是所有者
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        //下划线是一个特殊的字符，只用在一个函数修饰符种，它告诉 Solidity
        // 执行代码的其余部分。
        _;
    }

    //修饰符可以接受传参输入
    //这个修饰符检查传入的地址不是零地址。
    modifier validAddress(address _addr) {
        require(_addr != address(0), "Not valid address");
        _;
    }

    function changeOwner(address _newOwner) public onlyOwner validAddress(_newOwner) {
        owner = _newOwner;
    }

    //修改器可以在一个函数之前和/或之后被调用。
    // 这个修饰符可以防止一个函数在执行过程中被调用。
    // 它仍然在执行中。
    modifier noReentrancy() {
        // 最开始是false，取反为ture，继续执行
        require(!locked, "No reentrancy");

        locked = true;
        _; // 执行modify 调用的decrement 函数，当 i < 1的时候，停止循环
        locked = false; // 阻止循环
    }

    function decrement(uint i) public noReentrancy {
        x -= i;

        if (i > 1) {
            decrement(i - 1);
        }
    }
}

```

### 构造函数 constructor
构造函数（`constructor`）是一种特殊的函数，每个合约可以定义一个，并在部署合约的时候自动运行一次。它可以用来初始化合约的一些参数，例如初始化合约的`owner`地址：
```c
   // SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionModifier {
	address public owner;
    constructor() {
        // 将交易发送者设置为合约的所有者。
        owner = msg.sender;
    }
}
```

###  _ 是什么

看下图的代码 `_;` 就是将 decremet 代码在哪里插入运行

```c
//修改器可以在一个函数之前和/或之后被调用。
    // 这个修饰符可以防止一个函数在执行过程中被调用。
    // 它仍然在执行中。
    modifier noReentrancy() {
        // 最开始是false，取反为ture，继续执行
        require(!locked, "No reentrancy");

        locked = true;
        _; // 执行modify 调用的decrement 函数
        locked = false; // 阻止循环
    }

    function decrement(uint i) public noReentrancy {
        x -= i;

        if (i > 1) {
            decrement(i - 1);
        }
    }
```

深入参考：[# Solidity极简入门: 8. 构造函数和修饰器](https://github.com/AmazingAng/WTFSolidity/tree/main/08_Modifier)


## Event 事件
Event允许记录到以太坊区块链。Event的一些用例是：
1.监听事件和更新用户界面
2.一种费用更低的存储方式

`Solidity`中的事件（`event`）是`EVM`上日志的抽象，它具有两个特点：

-   响应：应用程序（[`ether.js`](https://learnblockchain.cn/docs/ethers.js/api-contract.html#id18)）可以通过`RPC`接口订阅和监听这些事件，并在前端做响应。
-   经济：事件是`EVM`上比较经济的存储数据的方式，每个大概消耗2,000 `gas`；相比之下，链上存储一个新变量至少需要20,000 `gas`。

### 规则

事件的声明由`event`关键字开头，然后跟事件名称，括号里面写好事件需要记录的变量类型和变量名。以`ERC20`代币合约的`Transfer`事件为例：

```c
event Transfer(address indexed from, address indexed to, uint256 value);
```

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Event {
    // 事件声明，前面带 event
	// 最多可以索引 3 个参数。
	// 被索引的参数可以帮助你通过被索引的indexed来过滤日志,查询更快
    event Log(address indexed sender, string message);
    event AnotherLog();

    function test() public {
        emit Log(msg.sender, "Hello World!");
        emit Log(msg.sender, "Hello EVM!");
        emit AnotherLog();
    }
}

```

输出如下

```json
[
    {
        "from": "0x9D7f74d0C41E726EC95884E0e97Fa6129e3b5E99",
        "topic": "0x0738f4da267a110d810e6e89fc59e46be6de0c37b1d5cd559b267dc3688e74e0",
        "event": "Log",
        "args": {
            "0": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4",
            "1": "Hello World!",
            "sender": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4",
            "message": "Hello World!"
        }
    },
    {
        "from": "0x9D7f74d0C41E726EC95884E0e97Fa6129e3b5E99",
        "topic": "0x0738f4da267a110d810e6e89fc59e46be6de0c37b1d5cd559b267dc3688e74e0",
        "event": "Log",
        "args": {
            "0": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4",
            "1": "Hello EVM!",
            "sender": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4",
            "message": "Hello EVM!"
        }
    },
    {
        "from": "0x9D7f74d0C41E726EC95884E0e97Fa6129e3b5E99",
        "topic": "0xfe1a3ad11e425db4b8e6af35d11c50118826a496df73006fc724cb27f2b99946",
        "event": "AnotherLog",
        "args": {}
    }
]
```


### 以ERC20代币为例
参考：[ERC20代币为例来介绍`solidity`中的事件（`event`）](https://github.com/AmazingAng/WTFSolidity/tree/main/09_Event)
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Events {
    // 定义一个 地址和余额的映射
    mapping(address => uint256) public _balance;
    // 定义了 event事件，该事件可以被 web3js/ether等订阅和监听
    event Transfer(address indexed from, address indexed to, uint256 value);

    // 定义一个 转账函数，执行转账逻辑
    function _trasfer(
        address from,
        address to,
        uint256 amount
    ) external {
        _balance[from] = 10000000;

        _balance[from] -= amount;
        _balance[to] += amount;

        emit Transfer(from, to, amount);
    }
}

```

测试执行的输出

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220624213332.png)



##  Constructor 构造函数
[# Solidity极简入门: 13. 继承 · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/13_Inheritance)
构造函数是一个可选的函数，在合约创建时被执行。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract X {
    string public name;

    constructor(string memory _name) {
        name = _name;
    }
}

contract Y {
    string public text;

    constructor (string memory _text) {
        text = _text;
    }
}

// 有2种方法可以用参数来初始化父合同。
// 在继承列表中把参数传到这里。

contract B is X("input to x"), Y("Input to Y"){}

contract C is X,Y {
    constructor(string memory _name, string memory _text) X(_name) Y(_text){}
}

/*
父构造函数总是按照继承的顺序被调用。
无论子合同的构造函数中列出的父合同的顺序如何，
父合同的构造函数总是按照继承顺序被调用。
下面调用的顺序
1.X
2.Y
3.D
*/

contract D is X,Y {
    constructor() X("x was called") Y("Y was callde") {}
}

// 下面的顺序Y在前面，先调用父构造函数，再调用子构造函数
// 1. X
// 2. Y
// 3. E
contract E is X, Y {
    constructor() Y("Y was called") X("X was called") {}
}
```

## Inheritance 继承 （is）
Solidity 支持多重继承。合约可以通过使用 is 关键字来继承其他合约。
可以被一个子合约覆盖的函数必须被声明为`virtual`
要覆盖一个父函数的函数必须使用关键字`override`
继承的顺序很重要。
你必须按照从 "最像基础 "到 "最像派生 "的顺序列出父合同。

### 规则
-   `virtual`: 父合约中的函数，如果希望子合约重写，需要加上`virtual`关键字。
-   `override`：子合约重写了父合约中的函数，需要加上`override`关键字。

### 多重继承
`solidity`的合约可以继承多个合约
例如：`contract D is X,Y` 重写在多个父合约中重名函数时，`override`关键字后面要加上所有父合约名字,`override(Yeye, Baba)`
```c
contract Erzi is Yeye, Baba{
    // 继承两个function: hip()和pop()，输出值为Erzi。
    function hip() public virtual override(Yeye, Baba){
        emit Log("Erzi");
    }

    function pop() public virtual override(Yeye, Baba) {
        emit Log("Erzi");
    }
```

### 修饰器的继承
`Solidity`中的修饰器（`Modifier`）同样可以继承，用法与函数继承类似，在相应的地方加`virtual`和`override`关键字即可。

```c
contract Base1 {
    modifier exactDividedBy2And3(uint _a) virtual {
        require(_a % 2 == 0 && _a % 3 == 0);
        _;
    }
}

contract Identifier is Base1 {

    //计算一个数分别被2除和被3除的值，但是传入的参数必须是2和3的倍数
    function getExactDividedBy2And3(uint _dividend) public exactDividedBy2And3(_dividend) pure returns(uint, uint) {
        return getExactDividedBy2And3WithoutModifier(_dividend);
    }

    //计算一个数分别被2除和被3除的值
    function getExactDividedBy2And3WithoutModifier(uint _dividend) public pure returns(uint, uint){
        uint div2 = _dividend / 2;
        uint div3 = _dividend / 3;
        return (div2, div3);
    }
}
```

上面的Identifier可以直接在代码中使用exactDividedBy2And3这个修饰器，也可以像下面一样重写修饰器。

```
    modifier exactDividedBy2And3(uint _a) override {
        _;
    }
```


```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* 继承关系图
    A
   / \
  B   C
 / \ /
F  D,E

*/

contract A {
    // 可以被子合约覆盖等方法，需要用virtural声明

    function foo() public pure virtual returns (string memory){
        return "A";
    }
}


contract B is A {
    // 重写 A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "B";
    }
}


contract C is A {
    // 重写 A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "C";
    }
}


/*
合同可以从多个父合同中继承。
当一个函数被调用时，它在不同的合同中被多次定义。
不同合约中多次定义的函数时，父合约将从从右到左，以深度优先的方式搜索。
*/

contract D is B, C {
    // D.foo() 返回 C
    // 因为 C 是具有函数 foo() 的最右边的父合约
    function foo() public pure override(B,C) returns (string memory){
        return super.foo();
    }
}


contract E is C, B {
    // E.foo() 返回 "B"
    // 因为B是最右边的父合同，有函数foo()。
    function foo() public pure override(C, B) returns (string memory) {
        return super.foo();
    }
}


// 继承必须从 "最基本 "到 "最派生 "排序。
// 交换 A 和 B 的顺序会抛出编译错误。 因为B是继承自A的
// 如果交换A和B，顺序变成B，A从右到左，A无法继承自B，B是A的派生
contract F is A, B {
    function foo() public pure override(A, B) returns (string memory) {
        return super.foo();
    }
}
```

## 继承重写变量  Shadowing Inherited State Variables

与函数不同，状态变量不能通过在子合约中重新声明来覆盖。
让我们学习如何正确覆盖继承的状态变量。
在构造函数中，将变量覆盖

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract A {
    string public name = "Contract A";

    function getName() public view returns (string memory) {
        return name;
    }
}

// Solidity 0.6 中不能重写
// 下面不能改变name变量
// contract B is A {
//     string public name = "Contract B";
// }

contract C is A {
    // 通过构造函数，能够正确重写变量
    constructor() {
        name = "Contract C";
    }

    // C.getName returns "Contract C"
}


```

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711162831.png)


## 调用父函数  Calling Parent Contracts
可以直接调用父合约，也可以使用关键字 super。
通过使用关键字 super，所有的直接父合约都会被调用。


### 完整代码
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* Inheritance tree
   A
 /  \
B   C
 \ /
  D
*/

contract A {
    // This is called an event. You can emit events from your function
    // and they are logged into the transaction log.
    // In our case, this will be useful for tracing function calls.
    // 这里会定义一个log事件，你可以在函数中调用它
    // 会记录 transaction的日志
    // 在这个案例中，它会记录方法的调用
    event Log(string message);

    function foo() public virtual {
        emit Log("A.foo called");
    }

    function bar() public virtual {
        emit Log("A.bar called");
    }
}

contract B is A {
    function foo() public virtual override {
        emit Log("B.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("B.bar called");
        super.bar();
    }
}

contract C is A {
    // override 是指继承的子合约可以重写该方法
    function foo() public virtual override {
        emit Log("C.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("C.bar called");
        super.bar();
    }
}

contract D is B, C {
    // Try:
    // - Call D.foo and check the transaction logs.
    //   Although D inherits A, B and C, it only called C and then A.
    // - Call D.bar and check the transaction logs
    //   D called C, then B, and finally A.
    //   Although super was called twice (by B and C) it only called A once.
    // 尝试调用D.foo 然后看转账的日志
    // 虽然 D 继承了 A、B 和 C，但它只调用了 C，然后调用了 A。
    // 调用 D.bar 并查看交易日志
    // D调用C，然后调用B，最后调用A。
    // 虽然 super 被调用了两次（B 和 C），但它只调用了 A 一次。

    function foo() public override(B, C) {
        super.foo();
    }

    function bar() public override(B, C) {
        super.bar();
    }
}

```
### 测试
#### A合约
```c
contract A {
    // This is called an event. You can emit events from your function
    // and they are logged into the transaction log.
    // In our case, this will be useful for tracing function calls.
    // 这里会定义一个log事件，你可以在函数中调用它
    // 会记录 transaction的日志
    // 在这个案例中，它会记录方法的调用
    event Log(string message);

    function foo() public virtual {
        emit Log("A.foo called");
    }

    function bar() public virtual {
        emit Log("A.bar called");
    }
}
```
执行A合约，定义了两个方法
* bar
* foo
输出 bar 或者foo
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711165400.png)

#### B合约
因为B合约继承了A合约
```c
contract B is A {
    function foo() public virtual override {
        emit Log("B.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("B.bar called");
        super.bar();
    }
}
```

调用foo()
先输出B.foo 再输出A.foo
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711165913.png)

调用bar()
先输出B.foo 再输出A.foo
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711170057.png)

#### C合约
```c
contract C is A {
    // override 是指继承的子合约可以重写该方法
    function foo() public virtual override {
        emit Log("C.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("C.bar called");
        super.bar();
    }
}
```

调用foo()
显示出c.foo 再输出 A.foo
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711170313.png)

调用bar()
显示出c.bar 再输出 A.bar
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711170351.png)

#### D合约
只调用了C和A，因为C和B的foo函数重复（都是A.foo()），只调用一个根据最右原则，只调用C的foo函数
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711172145.png)

D.bar调用了 C B A，因为都是用了super关键词，所以都会调用到

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220711172207.png)

## 可见性  Visibility
函数和状态变量必须声明它们是否可以被其他合约访问。
函数可以声明为
public - 任何合约和账户都可以调用
private - 仅在定义函数的合约内
internal - 仅继承内部功能的内部合同  **默认为internal**
external - 只有其他合约和账户可以调用
状态变量可以声明为公共的、私有的或内部的，但不能声明为外部的。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Base {
    // Private function can only be called
    // - inside this contract
    // Contracts that inherit this contract cannot call this function.
    // 私有函数只能被调用
    // - 在这个合约里面
    // 继承这个合约的合约不能调用这个函数。
    function privateFunc() private pure returns (string memory) {
        return "private function called";
    }

    function testPrivateFunc() public pure returns (string memory) {
        return privateFunc();
    }

    // Internal function can be called
    // - inside this contract
    // - inside contracts that inherit this contract
    // 内部函数可以被调用
    // - 在这个合同内部
    // - 继承此合同的合同内部
    function internalFunc() internal pure returns (string memory) {
        return "internal function called";
    }

    function testInternalFunc() public pure virtual returns (string memory) {
        return internalFunc();
    }

    // Public functions can be called
    // - inside this contract
    // - inside contracts that inherit this contract
    // - by other contracts and accounts
    // 可以调用公共函数
    // - 在这个合约里面
    // - 继承此合约的内部合约
    // - 通过其他合约和账户
    function publicFunc() public pure returns (string memory) {
        return "public function called";
    }

    // External functions can only be called
    // - by other contracts and accounts
    // 只能通过其他合约和账户 调用外部函数
    function externalFunc() external pure returns (string memory) {
        return "external function called";
    }

    // This function will not compile since we're trying to call
    // an external function here.
    // 当我们试图调用一个外部函数，这个函数不会编译。
    // function testExternalFunc() public pure returns (string memory) {
    //     return externalFunc();
    // }

    // State variables
    // 状态变量
    string private privateVar = "my private variable";
    string internal internalVar = "my internal variable";
    string public publicVar = "my public variable";
    // State variables cannot be external so this code won't compile.
    // string external externalVar = "my external variable";
}

contract Child is Base {
    // Inherited contracts do not have access to private functions
    // and state variables.
    // 继承的合约不能访问私有函数和私有状态变量。
    // function testPrivateFunc() public pure returns (string memory) {
    //     return privateFunc();
    // }

    // Internal function call be called inside child contracts.
    // 在子合约内部调用内部函数。
    function testInternalFunc() public pure override returns (string memory) {
        return internalFunc();
    }
}

```


## 接口 interface
你可以通过声明一个接口与其他合同进行交互。
接口类似于抽象合约，但它不实现任何功能。接口的规则：
1.  不能包含状态变量
2.  不能包含构造函数
3.  不能继承除接口外的其他合约
4.  所有函数都必须是external且不能有函数体
5.  继承接口的合约必须实现接口定义的所有功能

我们以`ERC721`接口合约`IERC721`为例，它定义了3个`event`和9个`function`，所有`ERC721`标准的NFT都实现了这些函数。我们可以看到，接口和常规合约的区别在于每个函数都以`;`代替函数体`{ }`结尾。

```c
interface IERC721 is IERC165 {
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
    
    function balanceOf(address owner) external view returns (uint256 balance);

    function ownerOf(uint256 tokenId) external view returns (address owner);

    function safeTransferFrom(address from, address to, uint256 tokenId) external;

    function transferFrom(address from, address to, uint256 tokenId) external;

    function approve(address to, uint256 tokenId) external;

    function getApproved(uint256 tokenId) external view returns (address operator);

    function setApprovalForAll(address operator, bool _approved) external;

    function isApprovedForAll(address owner, address operator) external view returns (bool);

    function safeTransferFrom( address from, address to, uint256 tokenId, bytes calldata data) external;
}
```

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Counter {
    uint public count;

    function increment() external {
        count += 1;
    }
}

interface ICounter {
    function count() external view returns (uint);

    function increment() external;
}

contract MyContract {
    function incrementCounter(address _counter) external {
        ICounter(_counter).increment();
    }

    function getCount(address _counter) external view returns (uint) {
        return ICounter(_counter).count();
    }
}

// Uniswap example
interface UniswapV2Factory {
    function getPair(address tokenA, address tokenB)
        external
        view
        returns (address pair);
}

interface UniswapV2Pair {
    function getReserves()
        external
        view
        returns (
            uint112 reserve0,
            uint112 reserve1,
            uint32 blockTimestampLast
        );
}

contract UniswapExample {
    address private factory = 0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f;
    address private dai = 0x6B175474E89094C44Da98b954EedeAC495271d0F;
    address private weth = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

    function getTokenReserves() external view returns (uint, uint) {
        address pair = UniswapV2Factory(factory).getPair(dai, weth);
        (uint reserve0, uint reserve1, ) = UniswapV2Pair(pair).getReserves();
        return (reserve0, reserve1);
    }
}

```


这个案例讲的太简单不太理解，补充一点wtf的知识
[Solidity极简入门: 14. 抽象合约和接口 | WTFSolidity-web](https://wtf.afox.cc/interface/#%E6%8A%BD%E8%B1%A1%E5%90%88%E7%BA%A6)


### 什么时候使用接口？
如果我们知道一个合约实现了IERC721接口，我们不需要知道它具体代码实现，就可以与它交互。

无聊猿BAYC属于ERC721代币，实现了IERC721接口的功能。我们不需要知道它的源代码，只需知道它的合约地址，用IERC721接口就可以与它交互，比如用balanceOf()来查询某个地址的BAYC余额，用safeTransferFrom()来转账BAYC。

```c
contract interactBAYC {
    // 利用BAYC地址创建接口合约变量（ETH主网）
    IERC721 BAYC = IERC721(0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D);

    // 通过接口调用BAYC的balanceOf()查询持仓量
    function balanceOfBAYC(address owner) external view returns (uint256 balance){
        return BAYC.balanceOf(owner);
    }

    // 通过接口调用BAYC的safeTransferFrom()安全转账
    function safeTransferFromBAYC(address from, address to, uint256 tokenId) external{
        BAYC.safeTransferFrom(from, to, tokenId);
    }
}
```


## 可支付  Payable
声明为payable的函数和地址可以在合约中接收以太币。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Payable {
    // Payable address can receive Ether
    // 定义收款地址可以接收以太币
    address payable public owner;

    // Payable constructor can receive Ether
    // Payable 构造函数可以接收 Ether
    constructor() payable {
        owner = payable(msg.sender);
    }

    // Function to deposit Ether into this contract.
    // Call this function along with some Ether.
    // The balance of this contract will be automatically updated.
    // 将以太币存入此合约的函数。
    // 调用这个函数，同时调用一些以太。
    // 该合约的余额将被自动更新。
    function deposit() public payable {}

    // Call this function along with some Ether.
    // The function will throw an error since this function is not payable.
    // 用一些以太来调用这个函数。
    // 该函数将抛出一个错误，因为该函数是不可支付的。
    function notPayable() public {}

    // Function to withdraw all Ether from this contract.
    // 从该合同中提取所有以太的功能。
    function withdraw() public {
        // get the amount of Ether stored in this contract
        // 获取存储在该合约中的以太币数量
        uint amount = address(this).balance;

        // send all Ether to owner
        // Owner can receive Ether since the address of owner is payable
        // 将所有以太币发送给所有者
        // 所有者可以收到以太币，因为所有者的地址是可支付的
        (bool success, ) = owner.call{value: amount}("");
        require(success, "Failed to send Ether");
    }

    // Function to transfer Ether from this contract to address from input
    // 将以太币从这个合约转移到输入地址的函数
    function transfer(address payable _to, uint _amount) public {
        // Note that "to" is declared as payable
        (bool success, ) = _to.call{value: _amount}("");
        require(success, "Failed to send Ether");
    }
}

```

## 发送eth  Sending Ether (transfer, send, call)
[Solidity极简入门: 20. 发送ETH transfer,send和call | WTFSolidity-web](https://wtf.afox.cc/sendeth/#%E6%8E%A5%E6%94%B6eth%E5%90%88%E7%BA%A6)
### 如何发送以太？
你可以通过以下方式向其他合约发送以太
* transfer (2300 gas, throws error)
* send（2300气体，返回bool）。
* call（转发所有气体或设置气体，返回bool）**最优解**
### 如何接收以太？
一个接收以太的合约必须至少有以下一个函数
* receive() 外部支付
* fallback() 对外支付
如果msg.data是空的，就调用receive()，否则就调用fallback()。
你应该使用哪种方法？
在2019年12月之后，推荐使用与重入防护相结合的调用方法。
通过以下方式防止重入
在调用其他合约之前进行所有的状态改变
使用re-entrancy guard修改器

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ReceiveEther {
    /*
    Which function is called, fallback() or receive()?

           send Ether
               |
         msg.data is empty?
              / \
            yes  no
            /     \
receive() exists?  fallback()
         /   \
        yes   no
        /      \
    receive()   fallback()
    */

    // Function to receive Ether. msg.data must be empty
    receive() external payable {}

    // Fallback function is called when msg.data is not empty
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}

contract SendEther {
    function sendViaTransfer(address payable _to) public payable {
        // This function is no longer recommended for sending Ether.
        _to.transfer(msg.value);
    }

    function sendViaSend(address payable _to) public payable {
        // Send returns a boolean value indicating success or failure.
        // This function is not recommended for sending Ether.
        bool sent = _to.send(msg.value);
        require(sent, "Failed to send Ether");
    }

    function sendViaCall(address payable _to) public payable returns (bytes memory data){
        // Call returns a boolean value indicating success or failure.
        // This is the current recommended method to use.
        (bool sent, bytes memory data) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
        return data;
    }

    function getBalance(address _address) public view returns (uint){
        uint customBalance = _address.balance;
        return customBalance;
    }
}

```


## 回退 Fallback
[Solidity极简入门: 19. 接收ETH receive和fallback | WTFSolidity-web](https://wtf.afox.cc/fallback/#%E6%8E%A5%E6%94%B6eth%E5%87%BD%E6%95%B0-receive)
Fallback是一个不接受任何参数也不返回任何东西的函数。
它在以下两种情况下被执行
1. 一个不存在的函数被调用
2. 以太被直接发送到一个合约，但receive()不存在，或者msg.data不是空的。
fallback在被transfer或send调用时有2300个气体限制。
```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Fallback {
    event Log(uint gas);

    // Fallback function must be declared as external.
    // 后备函数必须声明为外部的。
    fallback() external payable {
        // send / transfer (forwards 2300 gas to this fallback function)
        // call (forwards all of the gas)
        emit Log(gasleft());
    }

    // Helper function to check the balance of this contract
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}

contract SendToFallback {
    function transferToFallback(address payable _to) public payable {
        _to.transfer(msg.value);
    }

    function callFallback(address payable _to) public payable {
        (bool sent, ) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}

```

### fallback
`fallback()`函数会在调用合约不存在的函数时被触发。可用于接收ETH，也可以用于代理合约`proxy contract`。`fallback()`声明时不需要`function`关键字，必须由`external`修饰，一般也会用`payable`修饰，用于接收ETH:`fallback() external payable { ... }`。

我们定义一个`fallback()`函数，被触发时候会释放`fallbackCalled`事件，并输出`msg.sender`，`msg.value`和`msg.data`:

```c
    // fallback
    fallback() external payable{
        emit fallbackCalled(msg.sender, msg.value, msg.data);
    }
```



### receive和fallback的区别

`receive`和`fallback`都能够用于接收`ETH`，他们触发的规则如下：

```
触发fallback() 还是 receive()?
           接收ETH
              |
         msg.data是空？
            /  \
          是    否
          /      \
receive()存在?   fallback()
        / \
       是  否
      /     \
receive()   fallback()
```

简单来说，合约接收`ETH`时，`msg.data`为空且存在`receive()`时，会触发`receive()`；`msg.data`不为空或不存在`receive()`时，会触发`fallback()`，此时`fallback()`必须为`payable`。

`receive()`和`payable fallback()`均不存在的时候，向合约发送`ETH`将会报错。



## call函数
[Solidity极简入门: 21. 调用其他合约 | WTFSolidity-web](https://wtf.afox.cc/callcontract/#%E8%B0%83%E7%94%A8%E5%B7%B2%E9%83%A8%E7%BD%B2%E5%90%88%E7%BA%A6)
call是一个低级别的函数，用于与其他合约互动。
当你只是通过调用回落函数来发送以太币时，这是推荐使用的方法。
然而，它并不是调用现有函数的推荐方式。


```C
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
contract OtherContract {
    uint256 public _x = 0;
    event Log(uint amount, uint gas);

    function getBalance() view public returns(uint) {
        return address(this).balance;
    }

    function setX(uint256 x) external payable {
        _x = x;

        if(msg.value >0 ){
            emit Log(msg.value, gasleft());
        }
    }

    // 读取x
    function getX() external view returns(uint x){
        x = _x;
    }
}

contract CallContract {
    function callSetx(address _Address, uint256 x) external {
        OtherContract(_Address).setX(x);
    }

    // 传入合约地址，可以直接调用对应的方法
    function callGetX(OtherContract _Address) external view returns (uint x) {
        x = _Address.getX();
    }

    function callGetX2(address _Address) external view returns(uint x) {
        OtherContract oc = OtherContract(_Address);
        x = oc.getX();
    }

    function setXTransferETH(address otherContract, uint256 x) payable external {
        OtherContract(otherContract).setX{value: msg.value}(x);
    }
}
```

## Delegatecall
delegatecall 是一个类似于 call 的低级函数。
当合约A对合约B执行delegatecall时，执行B的代码
使用**合约 A 的存储，msg.sender 和 msg.value。**

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

//需要先部署B合约

contract B {
    // 如果需要调用，这里的存储结构必须一样
    uint public num;
    address public sender;
    uint public value;

    function setVars(uint _num) public payable {
        num = _num;
        sender = msg.sender;
        value = msg.value;
    }
}

contract A {
    uint public num;
    address public sender;
    uint public value;

    function setVars(address _contract, uint _num) public payable returns(bool, bytes memory) {
        // A合约的存储被设置，B的不会被改变
        (bool success, bytes memory data) = _contract.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
        return (success, data);
    }

    function setVars2(address _contract, uint _num) external {
        B(_contract).setVars(_num);
    }
}
```

分别部署A和B合约
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101338.png)

B合约地址 `0xD7ACd2a9FD159E69Bb102A1ca21C9a3e3A5F771B`
B合约num初始化为0
![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101517.png)

先测试`setVars2`,普通B方法，A的存储不会改变，只改变B的存储。
输入地址和数字，可以看到A合约的存储不会有任何改变，B的num变成了1

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101605.png)

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101647.png)


调用`setVars` 使用 `delegatecall` 方法，直接改变A方法的存储，B不会改变

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101821.png)

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716101831.png)



## 函数选择器   Function Selector
[Solidity极简入门: 29. 函数选择器Selector | WTFSolidity-web](https://wtf.afox.cc/selector/#selector)
调用函数时，calldata 的前 4 个字节指定调用哪个函数。
这 4 个字节称为函数选择器。
例如，下面的这段代码。它使用 call 在地址 addr 上执行合约的传输。
```c
addr.call(abi.encodeWithSignature("transfer(address,uint256)", 0xSomeAddress, 123))
```
从 abi.encodeWithSignature(....) 返回的前 4 个字节是函数选择器。
如果你在代码中预先计算并内联函数选择器，也许你可以节省少量的gas？
以下是函数选择器的计算方式。

```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionSelector {
    /*
    "transfer(address,uint256)"
    0xa9059cbb
    "transferFrom(address,address,uint256)"
    0x23b872dd
    */
    function getSelector(string calldata _func) external pure returns (bytes4) {
        return bytes4(keccak256(bytes(_func)));
    }
}

```

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716113738.png)


```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionSelector {

    // 返回msg.data
    event Log(bytes data);

    function mint(address to) external {
        emit Log(msg.data);
    }

    /*
    "transfer(address,uint256)"
    0xa9059cbb
    "transferFrom(address,address,uint256)"
    0x23b872dd
    */
    function getSelector(string calldata _func) external pure returns (bytes4) {
        return bytes4(keccak256(bytes(_func)));
    }
}
```

参数为：`0x2c44b726ADF1963cA47Af88B284C06f30380fC78`
可以看到data中的参数 `"data": "0x6a6278420000000000000000000000002c44b726adf1963ca47af88b284c06f30380fc78"`

方法名 mint(address) 前4个字节会被截取出来成为 `0x6a627842` 后面被参数补齐；

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220716114434.png)


## 调用其他合约 ## Calling Other Contract
合约可以通过 2 种方式调用其他合约。
最简单的方法就是直接调用它，
1. 比如 A.foo(x, y, z)。
2. 调用其他合约的另一种方法是使用`call`。不推荐这种方法。
```c
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;


// 需要先部署Callee合约
contract Callee {
    uint public x;
    uint public value;

    function setX(uint _x) public returns (uint) {
        x = _x;
        return x;
    }

    function setXandSendEhter(uint _x) public payable returns (uint,uint) {
        x = _x;
        value = msg.value;

        return (x, value);
    }
}

contract Caller {
    // 调用callee合约的x
    function setX(Callee _calledd, uint _x) public {
        uint x = _calledd.setX(_x);
    }
    // 通过地址方式调用合约x
    function setXFromAddress(address _address, uint _x) public {
        Callee callee = Callee(_address);
        callee.setX(_x);
    }
    // 通过call方式调用合约x
    function setXandSendEhter(Callee _callee, uint _x) public payable{
        (uint x,uint value) = _callee.setXandSendEhter{value:msg.value}(_x);
    }


}
```

## 使用合约创建合约 Contract that Creates other Contracts
其他合约可以使用 new 关键字创建合约。从 0.8.0 开始，new 关键字通过指定 salt 选项来支持 create2 功能。

[Solidity极简入门: 24. Create2 | WTFSolidity-web](https://wtf.afox.cc/create2/#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8create2)


