# sync.WaitGroup（等待组）类型

每个sync.WaitGroup值在内部维护着一个计数，此计数的初始默认值为零。

*sync.WaitGroup类型有三个方法：Add(delta int)、Done()和Wait()。

对于一个可寻址的sync.WaitGroup值wg，
   - 我们可以使用方法调用wg.Add(delta)来改变值wg维护的计数。
   - 方法调用wg.Done()和wg.Add(-1)是完全等价的。
   - 如果一个wg.Add(delta)或者wg.Done()调用将wg维护的计数更改成一个负数，一个恐慌将产生。
   - 当一个协程调用了wg.Wait()时，
       - 如果此时wg维护的计数为零，则此wg.Wait()此操作为一个空操作（no-op）；
       - 否则（计数为一个正整数），此协程将进入阻塞状态。 当以后其它某个
       - 协程将此计数更改至0时（一般通过调用wg.Done()），此协程将重新
       - 进入运行状态（即wg.Wait()将返回）。

一个*sync.WaitGroup值的Wait方法可以在多个协程中调用。 当对应的sync.WaitGroup值维护的计数降为0，这些协程都将得到一个（广播）通知而结束阻塞状态。

！[](images/39-1.png)

一个WaitGroup可以在它的一个Wait方法返回之后被重用。 但是请注意，当一个WaitGroup值维护的基数为零时，它的带有正整数实参的Add方法调用不能和它的Wait方法调用并发运行，否则将可能出现数据竞争。