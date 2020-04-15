- title: 离散对数抽象

## SUMMARY

`Karma` 中定义了一套基础的离散对数抽象，在此基础上，可以方便的对接各种基于离散对数的算法平台。同时此抽象层支持实现多套曲线标准。

## Motivation

提供基于离散对数的抽象层。

## Detailed design

### 标量运算

标量运算是再伽罗瓦域上实现的整数运算，整个数据集为 $S_p$ 其中，$p$ 指的是对应的伽罗瓦域的标量个数，往往是一个大整数。

标量运算包含几个基础的运算方式：加，乘，取反，取逆。同时包含基础的常量，分别为 $0$， $1$ ，以及一个关联的点类型。

```rust
pub trait ScalarNumber {
    /// Related point type.
    type Point: DisLogPoint;

    /// 1.
    const ZERO: Self;
    
    /// 0.
    const ONE: Self;

    /// add two scalar number.
    fn add(self, rhs: Self) -> Self;

    /// multiple two scalar number.
    fn mul(self, rhs: Self) -> Self;

    /// get inverse
    fn inv(self) -> Self;

    /// get negative
    fn neg(self) -> Self;
}
```

### 点运算

点运算是在伽罗瓦域上实现的椭圆曲线点运算，整个数据集为 $P_p$。

点运算包含几个基础的运算方式：加，乘标量，取反，取逆。同时包含基础的常量，分别为 $0$， $1$ ，以及一个关联的标量类型。

```rust
pub trait DisLogPoint {
    /// Related scalar type.
    type Scalar: ScalarNumber;

    /// 1.
    const ZERO: Self;
    
    /// 0.
    const ONE: Self;

    const GENERATOR: Self;

    /// add two point number.
    fn add(self, o: Self) -> Self;

    /// multiple two scalar number. 
    fn mul(self, o: Self::Scalar) -> Self;

    /// get inverse
    fn inv(self) -> Self;

    /// get negative
    fn neg(self) -> Self;
}
```

### 使用模式

对外提供数据提供的时候，需要通过点和标量类型进行使用。使用的时候类似于如下形式：

```rust
let p: Point<PointInner>;
let s: Scalar<ScalarInner>;
```

其中 `PointInner` 与 `ScalarInner` 需要实现上文所述的 `ScalarNumber` 和 `DisLogPoint` 两个trait。这两个类型 `Point` 和 `Scalar` 需要实现 `core` 中的运算符重载支持。