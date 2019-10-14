#### Pair<A , B>

~~~kotlin
fun main() {
    val (name, size) = getPair()
    println("$name   $size")

    getPair().let { (name, size) ->
        println("$name   $size")
    }
}

fun getPair(): Pair<String, Int> {
    return "WangHao" to 100
}
~~~

