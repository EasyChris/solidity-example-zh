---
sidebar: auto
pageClass: custom-page-class
---

# 前言

最近想深入学习一下 solidity，所以找了很多教程，发现 [Solidity by Example | 0.8.13](https://solidity-by-example.org/) 对于入门很好，但是只有英文版。中文版本 [GitHub - AmazingAng/WTFSolidity](https://github.com/AmazingAng/WTFSolidity) 写的非常详细，但是对于入门的选手有的章节有点难度，所以我就想结合两个一起做一下笔记，能够同时合并一下案例，将这些学习笔记一起开放出来。

# 学习小结

## 原始数据类型

在这里，我们向您介绍 Solidity 中可用的一些原始数据类型。

- `boolean`
- `uint`
- `int`
- `address`

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
例如 msg.sender

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
它们的值是硬编码的，使用常数可以节省 gas 成本。

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

交易是用 ether 支付的。
1ether 等于 10^18wei

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

_你需要为一笔交易支付多少 gas？_
你要支付花费的 gas = gas spend * gas price，其中 ether 一个计算单位
gas 消耗是交易中使用的 gas 总量
gas price 是你愿意为每个 gas 支付多少 ether 的单价
gas price 较高的交易有较高的优先权被纳入一个区块中。
未消耗的 gas 将被退还。
*Gas limit\*
你可以花费的 gas 量有两个上限

- gas limint（你愿意为你的交易使用的 gas，由你设定）
- block gas limit（一个区块中允许的最大 gas，由网络设置）

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

支持 if/else 也支持 三元运算符

函数后面 pure/view 表示不需要花费 gas

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

映射的创建语法是 mapping(keyType => valueType)。
keyType 可以是任何内置的值类型，字节，字符串，或任何合约。
valueType 可以是任何类型，包括另一个映射或一个数组。
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

先设置 myMap 的值，然后再用 get 获取

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

没有初始化的时候，默认是 false

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621154902.png)

设置过后，值发生了变化

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621155151.png)

直接调用 `nested` 的方法和 get 是一样的，因为 nested 是个 public，public 变量会自动生成 getter 函数用户查询 [WTFSolidity/03_Function at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/03_Function#solidity%E4%B8%AD%E7%9A%84%E5%87%BD%E6%95%B0)

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

### 测试 push 和 pop

push 一个数字之后，再调用 arr，就会输出刚才 push 的值

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621161845.png)

### 测试 address 数组

```c
     //定义address类型的数组
    address[] public addressArr;
    bytes public bytesArr;

    function setAddress() public returns (address[] memory){
        addressArr.push(msg.sender);
        return addressArr;
    }
```

输出如下，需要先调用 `setAddress` 后续调用数组下标为 0，输出就是当前的合约地址

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220621161635.png)

### 测试 example

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

### 数据位置

solidity 数据存储位置有三类：`storage`，`memory`和`calldata`。不同存储位置的`gas`成本不同。`storage`类型的数据存在链上，类似计算机的硬盘，消耗`gas`多；`memory`和`calldata`类型的临时存在内存里，消耗`gas`少。大致用法：

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
3.  `memory`赋值给`memory`，会创建引用，改变新变量会影响原变量。
4.  其他情况，变量赋值给`storage`，会创建独立的复本，修改其中一个不会影响另一个。

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

有几种方法可以从函数返回输出。
公共函数不能接受某些数据类型作为输入或输出

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

所以 x,y 为 4，6

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623151538.png)

## 函数的类型 view 和 pure

[WTFSolidity/03_Function at main · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/03_Function#solidity%E4%B8%AD%E7%9A%84%E5%87%BD%E6%95%B0)
Getter 函数可以声明为 view 或 pure。
View 函数声明不会更改任何状态。
纯函数声明不会更改或读取任何状态变量。
即 view 可以访问 contract 里定义的数据，而 pure 不可以。

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
你可以通过调用 require、revert 或 assert 抛出一个错误。

### require

require 用于在执行前验证输入和条件。当满足条件的时候，放行。

```c
// 当i大于10的时候正常运行
require(_i > 10, "Input must be greater than 10");
```

### revert

revert 与 require 类似。

```c
/ 当要检查的条件很复杂时，Revert 很有用。
        // 这段代码和上面的例子完全一样
        if (_i <= 10) {
            revert("Input must be greater than 10");
        }
```

### assert

assert 是用来检查那些不应该是错误的代码。断言失败可能意味着有一个错误。

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

### testRequire 输出

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623161332.png)

### testCustomError

自定义 error 搭配 revert 使用

可以看到该合约余额为 0，所以会抛出自定义错误

![](https://afox-1256168983.cos.ap-shanghai.myqcloud.com/20220623162549.png)

### 三种错误的 gas 比较

[WTFSolidity/13_Errors at main # 三种方法的 gas 比较 · AmazingAng/WTFSolidity · GitHub](https://github.com/AmazingAng/WTFSolidity/tree/main/13_Errors#%E4%B8%89%E7%A7%8D%E6%96%B9%E6%B3%95%E7%9A%84gas%E6%AF%94%E8%BE%83)

---

## 函数修饰符 Function Modifier

修饰符是可以在函数调用之前和/或之后运行的代码。
修饰符可用于： 1.限制访问 2.验证输入 3.防范重入黑客

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

### \_ 是什么

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

深入参考：[# Solidity 极简入门: 8. 构造函数和修饰器](https://github.com/AmazingAng/WTFSolidity/tree/main/08_Modifier)

## Event 事件

Event 允许记录到以太坊区块链。Event 的一些用例是： 1.监听事件和更新用户界面 2.一种费用更低的存储方式

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

### 以 ERC20 代币为例

参考：[ERC20 代币为例来介绍`solidity`中的事件（`event`）](https://github.com/AmazingAng/WTFSolidity/tree/main/09_Event)
