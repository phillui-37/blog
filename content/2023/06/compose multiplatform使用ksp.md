>timestamp: 2023-06-15T03:55:00+09:00
>
>tag: compose multiplatform, kotlin, gradle
>
>category: kotlin
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

PS: 找到了讓IDE能認出generated code的設定了
```kotlin
kotlin {
    sourceSets {
        val androidMain by getting {
            kotlin.srcDir("build/generated/ksp/android/androidDebug/kotlin")
            kotlin.srcDir("build/generated/ksp/android/androidRelease/kotlin")
        }
        val desktopMain by getting {
            kotlin.srcDir("build/generated/ksp/desktop/desktopMain/kotlin")
        }
    }
}
```

因為生成代碼不會出現在common(也沒有這dir),所以想在commonMain加`kotlin.srcDir`的得承擔代碼衝突的問題,更有可能會發生平台特定代碼被錯誤invoke的fatal error(反正過不了compiler),所以這邊就分成兩份了

至於android release/debug的問題,懶得找gradle doc了,這個大概不難解決

調用的話最好用actual/expect,別在common調用,雖然compiler給過,但ide會一直紅色error,也令intelliscene失去作用,雖然copy&paste也很白痴就是了