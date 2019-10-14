#### InputFilter

~~~kotlin
fun ContentFilter(regex: String): InputFilter {
        return InputFilter { source, _, _, _, _, _ ->
            val matcher = Pattern.compile(regex).matcher(source)
            if (matcher.find()) "" else null
        }
    }

InputFilter.LengthFilter(max)

editText.setFilters(filter : Array<InputFilter>)
~~~

#### Update SpellCheck

**使用反射调用`TextView`中`Editor`的`updateSpellCheckSpans`**

~~~kotlin
private fun initSpellCheckMethod() {
        val field = TextView::class.java.getDeclaredField("mEditor")
        field.isAccessible = true
        val editor = field.get(this)
        val method = editor.javaClass.getDeclaredMethod("updateSpellCheckSpans", Int::class.java, Int::class.java, Boolean::class.java)
        method.isAccessible = true
        method.invoke(editor, start, end, true)
    }
~~~

