- title: 密钥对生成

## SUMMARY

提供了基础的密钥对生成的功能。

## Motivation

非对称密码学中，密钥对的生成是重要的基础功能，这里需要提供一套基础的密钥对的生成功能。

## Detailed design

### Key pair generate

生成流程如下：

1. 随机生成一个种子`seed`，长度需要与私钥长度相同
2. 选定一个哈希函数H，要求哈希函数的输出为私钥长度的两倍
3. 将种子作为输入，计算哈希值 `o = H(seed)`
4. 将哈希值分为等长分为两部分，低位和高位
5. 低位作为私钥，乘生成元，得到公钥
6. 高位作为随机码

### Key pair represent and methods

最终生成的键值对结构体如下表示：

```rust
struct KeyPair<N: AsMut<[u8]> + Default + Sized, H: Hasher, P: DisLogPoint, S: SclarNumber> {
	seed: N,
    secret_key: Scalar<S>,
    public_key: Point<P>,
    code: N,
    _hash: PhantomData<Hasher>,
}
```

生成算法：

密钥对生成支持两种生成算法，可以从外部传入种子进行生成，也可以通过随机数生成器生成：

```rust
// 从随机数生成
fn generate<R: RngCore>(rng: R) -> Result<Self, Error> {}

// 从种子生成
fn generate_from_seed(seed: N) -> Result<Self, Error> {}
```

`get` 方法：

对结构体的访问需要提供每一个get方法。

