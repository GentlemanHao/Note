~~~kotlin
suspend fun main(args: Array<String>) {
    Start {
        println("start")
        println(Thread.currentThread())

        //getUser getCompany 顺序执行，一个成功再执行另一个
        val user = getUser()
        val company = getCompany()
        println(user + company)

        // getUser getCompany 同步执行，两个都成功再进行下一步
        val user = async { getUser() }
        val company = async { getCompany() }
        println(user.await() + company.await())
    }
}

private suspend fun getUser(): String? {
    return Io {
        println(Thread.currentThread())
        delay(2000)
        println("getUser end")
        return@Io "12345"
    }
}

private suspend fun getCompany(): String? {
    return Io {
        println(Thread.currentThread())
        delay(3000)
        println("getCompany end")
        return@Io "00000"
    }
}

~~~

简单封装
~~~kotlin
suspend fun Start(block: suspend CoroutineScope.() -> Unit) = coroutineScope {
    block.invoke(this)
}

suspend fun <T> Io(block: suspend CoroutineScope.() -> T): T? {
    var data: T? = null
    withContext(Dispatchers.IO) {
        data = block.invoke(this)
    }
    return data
}
~~~

