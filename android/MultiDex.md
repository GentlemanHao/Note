### MultiDex

~~~java
class ReflectUtil {

    public static Field findField(Object instance, String name) throws NoSuchFieldException {
        Class clazz = instance.getClass();
        while (clazz != null) {
            try {
                Field field = clazz.getDeclaredField(name);
                if (!field.isAccessible()) {
                    field.setAccessible(true);
                }
                return field;
            } catch (NoSuchFieldException e) {
                clazz = clazz.getSuperclass();
            }
        }
        throw new NoSuchFieldException("No such field... " + name);
    }

    public static Method findMethod(Object instance, String name, Class<?>... parameterTypes) throws NoSuchFieldException {
        Class clazz = instance.getClass();
        while (clazz != null) {
            try {
                Method method = clazz.getDeclaredMethod(name, parameterTypes);
                if (!method.isAccessible()) {
                    method.setAccessible(true);
                }
                return method;
            } catch (NoSuchMethodException e) {
                clazz = clazz.getSuperclass();
            }
        }
        throw new NoSuchFieldException("No such method... " + name);
    }
}
~~~

~~~java
class HotFixManager {

    private static final String FIXED_DEX_PATH = Environment.getExternalStorageDirectory().getPath() + "/fixed.dex";

    public static void installFixedDex(Context context) {
        try {
            //找到补丁包文件
            File fixedDexFile = new File(FIXED_DEX_PATH);

            if (!fixedDexFile.exists()) return;

            //获取PathClassLoader的pathList属性
            Field pathListField = ReflectUtil.findField(context.getClassLoader(), "pathList");
            Object dexPathList = pathListField.get(context.getClassLoader());

            //获取DexPathList中的makeDexElements方法
            Method makeDexElements = ReflectUtil.findMethod(dexPathList, "makeDexElements", List.class, File.class, List.class, ClassLoader.class);

            //把待加载的补丁文件添加到列表中
            ArrayList<File> filesToBeInstalled = new ArrayList<>();
            filesToBeInstalled.add(fixedDexFile);

            //准备makeDexElements方法调用所需参数
            File optimizedDirectory = new File(context.getFilesDir(), "fixed_dex");
            ArrayList<IOException> suppressedException = new ArrayList<>();

            //调用makeDexElements得到待修复Dex文件对应的Elements数组
            Object[] extraElements = (Object[]) makeDexElements.invoke(dexPathList, filesToBeInstalled, optimizedDirectory, suppressedException, context.getClassLoader());

            //获取原始Elements
            Field dexElementsField = ReflectUtil.findField(dexPathList, "dexElements");
            Object[] originElements = (Object[]) dexElementsField.get(dexPathList);

            //创建新的Elements
            Object[] combinedElements = (Object[]) Array.newInstance(originElements.getClass().getComponentType(), originElements.length + extraElements.length);

            //先放extraElements，再放originElements
            System.arraycopy(extraElements, 0, combinedElements, 0, extraElements.length);
            System.arraycopy(originElements, 0, combinedElements, extraElements.length, originElements.length);

            //替换原来的Elements数组
            dexElementsField.set(dexPathList, combinedElements);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
~~~

