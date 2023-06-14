>timestamp: 2023-04-26T12:03:00+08:00
>
>tag: backtracking, Leetcode
>
>category: Leetcode
>
>author: Phil Lui
>
>title: compose multiplatform使用ksp

在kotlin官網的ksp doc中有一頁[KSP with Kotlin Multiplatform](https://kotlinlang.org/docs/ksp-multiplatform.html) 寫了不能在build.gradle.kts中使用`ksp(...)`,要在module的build.gradle.kts的root scope寫dependencies,然後手動add進不同的**configuration**

對,不是module,是**configuration**,而且是你在加了ksp進plugins後用`configuration.forEach { println(it) }`才能看到...我先寫下我的config

```kotlin
// app:settings.gradle.kts
pluginManagement {
    plugins {
        id("com.google.devtools.ksp") version extra["ksp.version"] as String apply false
    }
}

// module:build.gradle.kts
plugins {
    id("com.google.devtools.ksp")
}

dependencies {
    arrayOf(
        "io.arrow-kt:arrow-optics-ksp-plugin:${Versions.arrow}"
    ).forEach {
        add("kspAndroid", it)
        add("kspCommonMainMetadata", it)
        add("kspDesktop", it)
    }
    add("kspAndroid", "androidx.room:room-compiler:${Versions.room}")
}
```

看來並不複雜,但要找到`kspXXX`那堆config的名字就是問題了

測試後是能正常compile的,run也沒問題,但idea和android studio都對ksp所產生的code沒反應,應該是要在sourceSets還是在dependencies中加上dir?這個要再研究...