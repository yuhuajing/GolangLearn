# 在 Golang 中，可以使用 math/rand 包中的函数来生成随机数。下面是一些示例：

生成一个随机的 int 类型的数：

```golang
import "math/rand"

// 生成一个 0 到 100 的随机整数
rand.Intn(100)
```

生成一个随机的 float64 类型的数：
```golang
import "math/rand"
// 生成一个 0.0 到 1.0 的随机浮点数
rand.Float64()
```

生成一个指定范围内的随机数：
```golang
import "math/rand"
import "time"

// 设置种子，使每次生成的随机数不同
rand.Seed(time.Now().Unix())

// 生成 10 到 20 的随机整数
rand.Intn(11) + 10

// 生成 0.0 到 10.0 的随机浮点数
rand.Float64() * 10.0
```

需要注意的是，在生成随机数时，需要先设置一个随机数种子。如果没有设置种子，每次生成的随机数都会是相同的。而使用 time.Now().Unix() 来设置种子可以保证每次生成的随机数都是不同的。

```golang
package main

import (
	"fmt"
	"math/rand"
)
func main() {
    s := rand.NewSource(time.Now().Unix())
	rand := rand.New(s)
	fmt.Println(rand.Intn(100))
}
```

# 在 Golang 中，可以使用 crypto/rand 包中的函数来生成安全的随机数。它使用的是加密级别的随机数生成器，可以生成安全性更高的随机数，适用于密码学等安全相关场景。

使用 crypto/rand 包生成指定区间内的随机数需要进行一些额外的处理，因为该包生成的随机数是具有高度随机性的字节数组，需要将其转换为整型并进行范围限制。

下面是一个示例代码，可以生成 0 到 10 的随机整数：
```golang
package main

import (
	"crypto/rand"
	"fmt"
	"math/big"
)

func main() {
	// 生成 0 到 10 的随机整数
	randInt, _ := rand.Int(rand.Reader, big.NewInt(11)) // 生成 0 到 10 的随机整数
	randomNum := randInt.Int64()

	fmt.Println(randomNum)
    //生成数组
    td, _ := GenerateRandomBytes(5)
	var ints = make([]int, len(td))
	for i, b := range td {
		ints[i] = int(b)
	}
	fmt.Println(ints)
}

func GenerateRandomBytes(n int) ([]byte, error) {
	b := make([]byte, n)
	_, err := rand.Read(b)
	if err != nil {
		return nil, err
	}
	return b, nil
}
```

在上面的代码中，我们首先使用 rand.Int() 从 crypto/rand 包中生成高度随机性的字节数组，并使用 big.NewInt() 创建一个包含 11 的 big.Int 类型的对象。然后调用 Int64() 方法，将该对象转换为 int64 类型，并将其转存到 randomNum 变量中。

需要注意的是，如果 crypto/rand 包在生成随机数时发生错误，上面的 rand.Int() 函数会返回一个 nil 的指针，这将导致上面的代码中的 _ 变量被赋值为 nil，所以代码中需要适当地处理此情况。