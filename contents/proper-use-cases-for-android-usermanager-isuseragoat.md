##安卓中“UserManger.isUserAGoat()”的合适案例？

###问题描述：
我正在看在安卓4.2里介绍的新API。当看到“UserManger”类时，我遇到了如下的“method”：
public boolean isUserAGoat()
Used to determine whether the user making this call is subject to teleportations.

Returns whether the user making this call is a goat.
这个应该怎样使用和什么时候使用？

###回答：
根据他们的资料，在API21前，这个“method”用来返回“false”
/**
 * Used to determine whether the user making this call is subject to
 * teleportations.
 * @return whether the user making this call is a goat 
 */
public boolean isUserAGoat() {
    return false;
}
看起来这个“method”对我们开发者没有真正的用途。一些人之前认为它可能是个复活节彩蛋。
再次编辑：
在API21它改为判断是否存在有包“com.coffeestainstudios.goatsimulator”的已安装app。
/**
 * Used to determine whether the user making this call is subject to
 * teleportations.
 *
 * <p>As of {@link android.os.Build.VERSION_CODES#LOLLIPOP}, this method can
 * now automatically identify goats using advanced goat recognition technology.</p>
 *
 * @return Returns true if the user making this call is a goat.
 */
public boolean isUserAGoat() {
    return mContext.getPackageManager()
            .isPackageAvailable("com.coffeestainstudios.goatsimulator");
}

[stackoverflow链接：Proper use cases for Android UserManager.isUserAGoat()](http://stackoverflow.com/questions/13375357/proper-use-cases-for-android-usermanager-isuseragoat)
