---
layout: blog 
android: true 
istop: true
title: "ReadHub项目Kotlin版开发指南(三、MVP架构)" 
background-image: https://ws2.sinaimg.cn/large/006tNc79ly1fzdaksqgo0j31930u0n40.jpg
date:  2019-03-17
category: Android
tags: 
- Android


---

[ReadHub项目Kotlin版转换指南(一、环境搭建)](<http://woong.cn/2019/01/21/readhub-kotlin1.html>)

[ReadHub项目Kotlin版转换指南(二、数据库和网络请求)](<http://woong.cn/2019/03/08/readhub-kotlin2.html>)

[ReadHub项目Kotlin版转换指南(三、MVP架构)](<http://woong.cn/2019/03/17/readhub-kotlin3.html>)

# ReadHub项目Kotlin版转换指南(三、MVP架构)

Android 开发中的 MVP 架构相信大家都已经熟悉，不熟悉的请右转 Google，ReadHub 项目从 Java 转换成 Kotlin 过程中，我们需要一套新的架构方式来实现（Kotlin 项目中没有使用 dagger2）。

base 为 MVP 架构的基础部分，user 为其具体使用过程。

## base

### BaseContract

```kotlin
interface BaseContract {
    interface IView {
        fun showLoading()
        fun hideLoading()
    }

    interface IPresenter<V : IView> {
        fun getView(): V
    }
}
```

`BaseContract` 中定义了两个接口 `IView` 和 `IPresenter`，

`IView` 接口中定义了 `Loading` 的操作，使其可以在每个 `View` 中使用，如果你的项目中有一些需要在大多数 `View` 中用到的函数，也可定义在此接口中。

IPresenter 中声明一个泛型 `V` 继承自 `IView`，通过 `getView` 函数可以获取到该变量，用来回调 Model 中的结果给 `View`。

### BaseActivity && BaseFragment

```kotlin
abstract class BaseActivity<P : BaseContract.IPresenter<*>> : AppCompatActivity(), BaseContract.IView {
    private var mKProgressHUD: KProgressHUD? = null
    protected lateinit var mPresenter: P

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val layoutId = getLayoutId()
        setContentView(layoutId)
        initView()
        initData()
    }

    protected abstract fun getLayoutId(): Int
    protected abstract fun initView()
    protected abstract fun initData()

    override fun showLoading() {
        mKProgressHUD = KProgressHUD.create(this)
        mKProgressHUD?.setStyle(KProgressHUD.Style.SPIN_INDETERMINATE)
                ?.setCancellable(true)
                ?.setAnimationSpeed(2)
                ?.setDimAmount(0.5f)
                ?.show()
    }

    override fun hideLoading() {
        mKProgressHUD?.dismiss()
    }
}
```

```kotlin
abstract class BaseFragment<P : BaseContract.IPresenter<*>> : Fragment(), BaseContract.IView {
    private var mRootView: View? = null
    private var mKProgressHUD: KProgressHUD? = null
    protected lateinit var mPresenter: P

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        inflaterView(inflater, container)
        return mRootView
    }

    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        initView(mRootView)
        initData()
    }

    /**
     * 设置View
     *
     * @param inflater
     * @param container
     */
    private fun inflaterView(inflater: LayoutInflater, @Nullable container: ViewGroup?) {
        if (mRootView == null) {
            mRootView = inflater.inflate(getLayoutId(), null)
        }
    }

    protected abstract fun getLayoutId(): Int
    protected abstract fun initView(view: View?)
    protected abstract fun initData()

    override fun showLoading() {
        mKProgressHUD = KProgressHUD.create(activity)
        mKProgressHUD?.setStyle(KProgressHUD.Style.SPIN_INDETERMINATE)
                ?.setCancellable(true)
                ?.setAnimationSpeed(2)
                ?.setDimAmount(0.5f)
                ?.show()
    }

    override fun hideLoading() {
        mKProgressHUD?.dismiss()
    }
}
```

`BaseActivity` 中声明泛型 `P` 继承自 `BaseContract.IPresenter`，变量 `mPresenter` 声明类型 `P` 并且延迟初始化（在子类中初始化）。

`BaseActivity` 继承 `BaseContract.IView`，重写回调方法，如 `hideLoading、showLoading`。

##  news

### NewsContract

```kotlin
interface NewsContract {
    interface View : BaseContract.IView {
        fun updateTechNews(publishDate: String, newsMos: ArrayList<NewsMo>)
    }

    interface Presenter : BaseContract.IPresenter<View> {
        fun getTechNews(publishDate: String)
    }
}
```

### NewsPresenter

```kotlin
class NewsPresenter(var mView: BaseContract.IView) : NewsContract.Presenter {
    override fun getView(): NewsContract.View {
        return mView as NewsContract.View
    }

    override fun getTechNews(publishDate: String) {
        LogUtils.i("apiTachNews === ")
        App.sInstance.apiService(ReadhubApiService::class.java)
                ?.apiTeachNews(publishDate, Constant.NEWS_PAGE_SIZE)
                ?.compose(RxSchedulers.io_main())
                ?.subscribe( {
                    getView()?.updateTechNews(publishDate, it.data!!)
                }, {
                    LogUtils.e("apiTeachNews error == ${it}")
                })

    }
}
```

`getView` 获取 `IView` 对象的实现。

### NewsFragment

```
class NewsFragment : BaseFragment<NewsPresenter>(), NewsContract.View {
    override fun getLayoutId(): Int {
        return R.layout.fragment_news
    }

    override fun initView(view: View?) {
    }

    override fun initData() {
        mPresenter = NewsPresenter(this)
        mPresenter?.getTechNews("")
    }

    override fun updateTechNews(publishDate: String, newsMos: ArrayList<NewsMo>) {}
}
```

构造 `mPresenter` 对象，获取 `Model` 中的数据。