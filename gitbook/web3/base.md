DAPP：web3前端app

Smart Contract：智能合约，WEB3.0后端服务

区块链：利用区块链技术使得这些电脑能够连在一起

虚拟货币

在线运行

https://remix.ethereum.org/#lang=en&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.18+commit.87f61d96.js

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/120075/28/23022/41738/64af6a08Fff12d620/e02f506a67345962.jpg)

每个电脑上的交易transactions打包成block，连上其他电脑就是blockchain





![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/147456/4/37832/18408/64af6cc6Ff919373e/6b8257dd1ea50316.jpg)

https://andersbrownworth.com/blockchain/block

在blockchain中第一个块叫创世块-genesis，但是刚创建出来没有交易

当我们一产生block，比特币就会给我们奖励多少钱，这就是挖矿



sha256（区块 + 随机数 + 数据） = hash

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/200206/23/38329/16475/64af706fFfe91ce2e/7f5262da8384d803.jpg)

所以挖矿，就是当hash相等时，就会给比特币

https://andersbrownworth.com/blockchain/blockchain

挖矿就是我在数据处输入hi之后，通过修改随机数，使得hash以000开头，这样就是有效的，就挖到了，否则就没挖到

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/131841/3/38344/17588/64afb03fFc3a1c466/c0e54dbd44ad3f34.jpg)

如下，这样当我输入59396时就以000开头了，这就是挖到了区块

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/215333/9/32924/16745/64afb0caF25a177db/1bc32393c8f599f1.jpg)

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/207849/16/36193/46810/64af71b7F00531d7f/c809f7d8259837ca.jpg)

具体交易

https://andersbrownworth.com/blockchain/tokens

这里的其实也是一串hash，为了表示才写成的字母

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/160410/37/38609/66901/64af72acF20678e1c/b3d1ed6addc6201c.jpg)

所以交易是链子上的每台电脑都有，就是为了防止有人改了其中一个

我们可以查到地址交易，例如

~~~btc
0xdasd12n31ndasldj123213 -> 0xdasddddddasdasdq123ndasd   999btc
~~~

我们的每一个block上存的是交易

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/96987/40/37966/6346/64af74c1F240d1cb5/c8c8e999197fea14.jpg)

我们的这里的数据可以存放任何内容，比如a给b转账，或者调用了xxx函数

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/99124/36/40954/38768/64af74cdF6c191556/71e8c37b06b4a321.jpg)

比如，你可以放一个hello world

这个就相当于一个后端，就是智能合约（可以调用函数，或者返回字符串什么的）

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/198119/28/37361/38627/64af7563Fdc4bf987/2adfd9ff0d8f8fc3.jpg)

也可以在智能合约内再产生新block

Solidity在线

https://remix.ethereum.org/

钱包

https://etherscan.io/

分布式

https://andersbrownworth.com/blockchain/distributed

实际中就是这种分布式的，这里提供了节点A，节点B，节点C

我修改了节点B的倒数第二个区块，那么最后的地址变成了

~~~
0000f1a0b78764d8ce33a4e068c2c25700871bb854dd4297ea6ec14bbc37fe48
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/209538/5/36789/45737/64afb389F29e99dc8/c8687110d1d8696a.jpg)

而节点A和B的地址是：

~~~
0000e4b9052fd8aae92a8afda42e2ea0f17972ea67cead67352e74dd6f7d217c
~~~

这两者完全不一样，所以证明B被修改了，而且因为全世界节点众多，所以攻击者无法控制超过一半的节点来伪造数据，这就是去中心化的好处，以多打少

模拟代币交易

https://andersbrownworth.com/blockchain/tokens

我这里将其改为lisa，想将钱转给lisa，重新校验

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/111064/23/42607/56243/64afb445F5e934f08/5d09d04fc6ca73e4.jpg)
![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/6593/7/19076/41011/64afb467F37b7c107/b4fc8ae977ab4308.jpg)

这里B节点的hash是：

~~~
0000b7772e19aa1b42ed9d23f103519fc08c660dbb8d0028dac84f6dbff25889
~~~

A和C的是：

~~~
0000baa0cce9eb9432ce5b3081624be9fe47700fbe75bfd3bd276a03cc0dbe3a
~~~

证明B被篡改过

但是这种的是只记录转账过程，例如吉米给了丽萨25美元，但是不记录他们最初有多少钱，如果吉米没有25美元，如何给丽萨25美元呢？

币基

https://andersbrownworth.com/blockchain/coinbase

以太坊是一条区块链，其中嵌入了计算机。 它是以去中心化、无需许可、抗审查的方式构建应用程序和组织的基础。

**以太币 (ETH)** 是以太坊上的原生加密货币。 以太币的目的是允许计算市场化。



### Block结构

https://andersbrownworth.com/blockchain/blockchain

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/105869/21/36710/54099/64d49717F39a9195c/5ef45b0f98960ce7.jpg)

从面向对象的角度思考，这里每一行就是一个区块链对象，区块链里有一个数组，这个数组存储了存储了所有的区块

我的golang版本是：`1.21.0`

创建如下目录

~~~
publicChain
	part1-Basic-Prototype
		BLC
			Block.go
			BlockChain.go
		main.go	
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/98804/25/43247/37988/64d4aa38Fcdb74822/41cf784306d71e4f.jpg)

这里为了import导入时是这个BLC下的文件，采用./的方式

main.go

~~~go
package main

import (
	"./BLC"
	"fmt"
)

func main() {
	block := BLC.NewBlock("batmanfuture", 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
	fmt.Println(block)

}
~~~

Block.go

~~~go
package BLC

import "time"

type Block struct {

	//1.区块高度
	Height int64
	//2.上一个区块hash
	PrevBlockHash []byte
	//3.交易数据
	Data []byte
	//4.时间戳
	Timestamp int64
	//5.hash
	Hash []byte
}

//1.create new block
func NewBlock(data string, height int64, PrevBlockHash []byte) *Block {

	block := &Block{height, PrevBlockHash, []byte(data), time.Now().Unix(), nil}
	return block
}

~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/205422/32/32811/51192/64d4abebF1a9aba2b/164d89c6a9920927.jpg)

。

### 根据区块数据生成区块HASH

上一步我们的上一个hash是0000000000000000000000，当前hash是nil，也是0，我们令当前hash为

`Height`，`Timestamp`   ，`PrevBlockHash`，`Data`这四部分组合而成

~~~
publicChain
	part1-Basic-Prototype
		BLC
			Block.go
			BlockChain.go
			utils.go
		main.go	
~~~

这里utils.go代码是：

~~~go
package BLC

import (
	"bytes"
	"encoding/binary"
	"log"
)

// IntToHex converts an int64 to a byte array
func IntToHex(num int64) []byte {
	buff := new(bytes.Buffer)
	err := binary.Write(buff, binary.BigEndian, num)
	if err != nil {
		log.Panic(err)
	}

	return buff.Bytes()
}

// ReverseBytes reverses a byte array
func ReverseBytes(data []byte) {
	for i, j := 0, len(data)-1; i < j; i, j = i+1, j-1 {
		data[i], data[j] = data[j], data[i]
	}
}
~~~

block.go

~~~go
package BLC

import (
	"bytes"
	"crypto/sha256"
	"fmt"
	"strconv"
	"time"
)

type Block struct {

	//1.区块高度
	Height int64
	//2.上一个区块hash
	PrevBlockHash []byte
	//3.交易数据
	Data []byte
	//4.时间戳
	Timestamp int64
	//5.hash
	Hash []byte
}

func (block *Block) SetHash() {
	heightbytes := IntToHex(block.Height)
	fmt.Println(heightbytes)
	// 这里2就是指代将时间戳转换为2进制
	timeString := strconv.FormatInt(block.Timestamp, 2)
	//fmt.Println(timeString)
	timestamp := []byte(timeString)
	fmt.Println(timestamp)

	blockBytes := bytes.Join([][]byte{heightbytes, block.PrevBlockHash, block.Data, timestamp, block.Hash}, []byte{})
	//fmt.Println(blockBytes)

	hash := sha256.Sum256(blockBytes)
	fmt.Println(hash)

	block.Hash = hash[:]
}

// 1.create new block
func NewBlock(data string, height int64, PrevBlockHash []byte) *Block {

	block := &Block{height, PrevBlockHash, []byte(data), time.Now().Unix(), nil}
	block.SetHash()
	return block
}
~~~

这里SetHash方法就是计算当前hash，当前hash由高度的bytes，上一个地址hash的bytes，交易的bytes，时间戳的bytes，当前hash的bytes拼接在一起，再计算sha256，然后截取32位得到

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/113363/17/39629/47652/64d4b417F1aeaec72/bc1a747231724ed6.jpg)

### CreateGenesisBlock

 写创世区块，就是第一个区块

main.go

~~~go
package main

import (
	"./BLC"
	"fmt"
)

func main() {
	//block := BLC.NewBlock("batmanfuture", 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
	//fmt.Println(block)

	genesisBlock := BLC.CreateGenesisBlock("Genenis is Block.............")
	fmt.Println(genesisBlock)

}
~~~

Block.go

~~~go
package BLC

import (
	"bytes"
	"crypto/sha256"
	"fmt"
	"strconv"
	"time"
)

type Block struct {

	//1.区块高度
	Height int64
	//2.上一个区块hash
	PrevBlockHash []byte
	//3.交易数据
	Data []byte
	//4.时间戳
	Timestamp int64
	//5.hash
	Hash []byte
}

func (block *Block) SetHash() {
	heightbytes := IntToHex(block.Height)
	fmt.Println(heightbytes)
	// 这里2就是指代将时间戳转换为2进制
	timeString := strconv.FormatInt(block.Timestamp, 2)
	//fmt.Println(timeString)
	timestamp := []byte(timeString)
	fmt.Println(timestamp)

	blockBytes := bytes.Join([][]byte{heightbytes, block.PrevBlockHash, block.Data, timestamp, block.Hash}, []byte{})
	//fmt.Println(blockBytes)

	hash := sha256.Sum256(blockBytes)
	fmt.Println(hash)

	block.Hash = hash[:]
}

// 1.create new block
func NewBlock(data string, height int64, PrevBlockHash []byte) *Block {

	block := &Block{height, PrevBlockHash, []byte(data), time.Now().Unix(), nil}
	block.SetHash()
	return block
}

func CreateGenesisBlock(data string) *Block {

	return NewBlock(data, 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
}
~~~

如下，新增了CreateGenesisBlock方法，这里就是第一个区块只需要交易数据，高度默认是1，上一个hash地址都是0，然后main.go中调用即可

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/220178/4/27712/75936/64d4b702Fc757013b/24f7e195ae14b0e7.jpg)

。

### Blockchain结构

~~~
part3-Basic-Prototype
	BLC
		Block.go
		BlockChain.go
		utils.go
	main.go
~~~

main.go

~~~go
package main

import (
	"./BLC"
	"fmt"
)

func main() {
	//block := BLC.NewBlock("batmanfuture", 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
	//fmt.Println(block)

	genesisBlock := BLC.CreateBlockchainWithGenesisBlock()
	fmt.Println("打印当前区块链: ", genesisBlock)

	fmt.Println("打印当前区块链的创世区块地址: ", genesisBlock.Block)
	fmt.Println("打印当前区块链的第1个区块: ", genesisBlock.Block[0])
	fmt.Println("打印当前区块链的第2个区块: ", genesisBlock.Block[1])

}

~~~

BlockChain.go

~~~go
package BLC

type Blockchain struct {
	Block []*Block // 存储有序的区块
}

func CreateBlockchainWithGenesisBlock() *Blockchain {
	genesisBlock := CreateGenesisBlock("batmanfuture is this")
	//fmt.Println(genesisBlock)
	return &Blockchain{[]*Block{genesisBlock}}
}

~~~

这里CreateBlockchainWithGenesisBlock函数通过return指向结构体的指针方式可以理解为对Blockchain类进行实例化，所以return &就是实例化的过程，内部实现对创世区块的创建

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/169885/22/39915/82613/64d4bfe5F71d8f89f/2d1f7969d7eedfbe.jpg)

。

### 添加新的区块

上个代码我们可以创建区块链了，但是这个区块链只有创世区块这一个区块，我们新的功能是可以对这个区块链增加新的区块

main.go

~~~go
package main

import (
	"./BLC"
	"fmt"
)

func main() {
	//block := BLC.NewBlock("batmanfuture", 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
	//fmt.Println(block)

	blockchain := BLC.CreateBlockchainWithGenesisBlock()
	fmt.Println("打印当前区块链: ", blockchain)

	fmt.Println("打印当前区块链的创世区块地址: ", blockchain.Block)
	fmt.Println("打印当前区块链的第1个区块: ", blockchain.Block[0])

	blockchain.AddBlockToBlockchain("111111111111111", blockchain.Block[len(blockchain.Block)-1].Height+1, blockchain.Block[len(blockchain.Block)-1].Hash)
	blockchain.AddBlockToBlockchain("222222222222222", blockchain.Block[len(blockchain.Block)-1].Height+1, blockchain.Block[len(blockchain.Block)-1].Hash)
	blockchain.AddBlockToBlockchain("333333333333333", blockchain.Block[len(blockchain.Block)-1].Height+1, blockchain.Block[len(blockchain.Block)-1].Hash)
	blockchain.AddBlockToBlockchain("444444444444444", blockchain.Block[len(blockchain.Block)-1].Height+1, blockchain.Block[len(blockchain.Block)-1].Hash)

	fmt.Println("打印当前区块链的第2个区块: ", blockchain.Block[1])
	fmt.Println("打印当前区块链的第3个区块: ", blockchain.Block[2])
	fmt.Println("打印当前区块链的第4个区块: ", blockchain.Block[3])
	fmt.Println("打印当前区块链的第5个区块: ", blockchain.Block[4])

}

~~~



blockchain.go

~~~go
package BLC

type Blockchain struct {
	Block []*Block // 存储有序的区块
}

func (blc *Blockchain) AddBlockToBlockchain(data string, height int64, preHash []byte) {
	//创建新的区块
	newBlock := NewBlock(data, height, preHash)
	//往链中添加区块
	blc.Block = append(blc.Block, newBlock)
}

func CreateBlockchainWithGenesisBlock() *Blockchain {
	genesisBlock := CreateGenesisBlock("batmanfuture is this")
	//fmt.Println(genesisBlock)
	return &Blockchain{[]*Block{genesisBlock}}
}

~~~

在blockchain.go中，我们新建了一个func，这个func是Blockchain结构体的实例化，用在增加区块，接收参数是当前区块所交易的数据，高度，前一个地址hash，newBlock := NewBlock(data, height, preHash)实现产生一个新的区块，并blc.Block = append(blc.Block, newBlock)增加到这个区块链

在main.go中

`blockchain.AddBlockToBlockchain("hhhhhhhhhhhhhhh", blockchain.Block[len(blockchain.Block)-1].Height+1, blockchain.Block[len(blockchain.Block)-1].Hash)`

第二个参数，就是判断当前已有多少个区块，例如，只有一个创世区块，那么就-1再+1得到1，新的区块高度就是1，而第三个参数

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/111672/25/41297/90031/64d59eb4F689bf909/87b11f8eb82cc336.jpg)

。

### ProofOfWork框架

为了实现工作量证明，修改Block结构体

block.go

~~~go
package BLC

import (
	"bytes"
	"crypto/sha256"
	"fmt"
	"strconv"
	"time"
)

type Block struct {

	//1.区块高度
	Height int64
	//2.上一个区块hash
	PrevBlockHash []byte
	//3.交易数据
	Data []byte
	//4.时间戳
	Timestamp int64
	//5.hash
	Hash []byte
	//6.Nonce
	Nonce int64
}

func (block *Block) SetHash() {
	heightbytes := IntToHex(block.Height)
	fmt.Println(heightbytes)
	// 这里2就是指代将时间戳转换为2进制
	timeString := strconv.FormatInt(block.Timestamp, 2)
	//fmt.Println(timeString)
	timestamp := []byte(timeString)
	fmt.Println(timestamp)

	blockBytes := bytes.Join([][]byte{heightbytes, block.PrevBlockHash, block.Data, timestamp, block.Hash}, []byte{})
	//fmt.Println(blockBytes)

	hash := sha256.Sum256(blockBytes)
	fmt.Println(hash)

	block.Hash = hash[:]
}

// 1.create new block
func NewBlock(data string, height int64, PrevBlockHash []byte) *Block {

	block := &Block{height, PrevBlockHash, []byte(data), time.Now().Unix(), nil, 0}

	//调用工作量证明的方法返回有效的Hash和Nonce
	pow := NewProofOfWork(block)
	hash, nonce := pow.Run()
	block.Hash = hash[:]
	block.Nonce = nonce

	return block
}

func CreateGenesisBlock(data string) *Block {

	return NewBlock(data, 1, []byte{0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0})
}

~~~



ProofOfWork.go

~~~go
package BLC

type ProofOfWork struct {
	Block *Block
}

func (proofOfWork *ProofOfWork) Run() ([]byte, int64) {
	return nil, 0
}

func NewProofOfWork(block *Block) *ProofOfWork {
	return &ProofOfWork{block}
}

~~~



这里我们引入了任务量，Nonce int64，添加到结构体中，该任务量的作用就是用来计算hash，而不是用SetHash方法了，在ProofOfWork.go中，我们定义一个结构体



### 工作量证明算法分析





































