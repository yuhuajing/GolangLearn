# Why the Time ticket need the <-t.C after Stop the ticket channle

```golang
func (p *pool) refreshPool() {
	t := time.NewTimer(0)
	started := sync.Once{}
	for {
		select {
		case <-t.C:
			p.doRefresh()
			started.Do(func() {
				close(p.started)
			})

			t.Reset(p.config.PoolRefresh)
		case <-p.refresh:
			p.doRefresh()
			started.Do(func() {
				close(p.started)
			})

			if !t.Stop() {
				<-t.C
			}
			t.Reset(p.config.PoolRefresh)
		case <-p.done:
			return
		}
	}
}

# time.NewTimer() 函数会返回一个计时器 Timer 类型的值，代表一个定时器。当计时器的定时时间超时时，计时器会向其内部的通道 C 发送一个时间值。

Timer.Stop() 方法可以用来停止计时器。但是，如果在调用 Stop() 方法之前，计时器已经超时并向通道 C 发送了超时时间，该超时时间仍然会在通道 C 中等待被接收。因此，为了防止 Stop() 调用之后，程序仍然在等待通道的发送数据而出现阻塞的情况，可以使用 <-t.C 语句来接收通道中尚未处理的超时时间。

因此，time.NewTimer.Stop(){<-t.C} 的意思是，在执行 Stop() 方法停止计时器后，使用 <-t.C 语句阻塞等待接收通道 C 中尚未处理的超时时间，以确保不会出现通道阻塞等问题。其中，t 是 time.NewTimer() 函数返回的计时器类型的值。

