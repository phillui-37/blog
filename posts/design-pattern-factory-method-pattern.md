<!--
.. title: Design Pattern - Factory Method Pattern
.. slug: design-pattern-factory-method-pattern
.. date: 2024-10-06 14:17:07 UTC+08:00
.. tags: design-pattern,chinese
.. category: programming
.. link: 
.. description: 
.. type: text
-->

## 工廠模式 - Factory Method Pattern

用途: 將建立實體的邏輯放於其規格內部,以達到封裝及資源管理的目的.

例子:

```Java
abstract class Logger {
    protected int logLevel;
    Logger(int logLevel) {
        this.logLevel = logLevel;
    }

    static Logger getInstance() {
        return getInstance(3); //info
    }

    static Logger getInstance(int logLevel) {
        if (env == "DEBUG") {
            return new DebugLogger(logLevel);
        }
        return new ReleaseLogger(logLevel);
    }

    abstract int verbose(String msg);
    abstract int debug(String msg);
    abstract int info(String msg);
    abstract int warn(String msg);
    abstract int error(String msg);
    abstract int fatal(String msg);
    abstract int getLogLevel();
    abstract void setLogLevel(int logLevel);
}

class DebugLogger implements Logger {
    DebugLogger(int logLevel) {
        super(logLevel);
    }

    @Override
    int verbose(String msg) {
        System.err.println(msg);
        return 0;
    }
    ...
}

class ReleaseLogger implements Logger {
    ReleaseLogger(int logLevel) {
        super(logLevel);
    }

    @Override
    int verbose(String msg) {
        if (this.logLevel <= 0)
            System.err.println(msg);
        return 0;
    }

    @Override
    int debug(String msg) {
        if (this.logLevel <= 1)
            System.err.println(msg);
        return 0;
    }
    ...
}
```

```javascript
const DEFAULT_LOG_LEVEL = 3;
const AVAILABLE_LOG_LEVEL = [
    'trace',
    'debug',
    'log',
    'info',
    'warn',
    'error'
];

const releaseLogger = (() => {
    let logLevel;

    let logFnMeta = (level) => {
        if (logLevel <= level)
            return (msg) => console[level](msg);
        else
            return (_) => {};
    };

    const getLogFns = () => AVAILABLE_LOG_LEVEL.reduce((acc, level) => {
        return {...acc, [level]: logFnMeta(level)};
    }, {});

    let loggerInstance = getLogFns();

    const setLogLevel = (_logLevel) => {
        logLevel = _logLevel;
        loggerInstance = getLogFns();
    };

    return (_logLevel) => {
        logLevel = _logLevel ?? DEFAULT_LOG_LEVEL;
        return {
            ...loggerInstance,
            setLogLevel,
            getLogLevel: () => logLevel,
        };
    };
})();

const debugLogger = (() => {
    const loggerInstance = AVAILABLE_LOG_LEVEL.reduce((acc, level) => {
        return {...acc, [level]: (msg) => console[level](msg)};
    }, {});

    return (_logLevel) => {
        let logLevel = _logLevel ?? DEFAULT_LOG_LEVEL;
        return {
            ...loggerInstance,
            setLogLevel: (_logLevel) => {logLevel = _logLevel},
            getLogLevel: () => logLevel,
        };
    };
})();

const logger = (logLevel) => {
    if (env === 'DEBUG')
        return debugLogger(logLevel);
    return releaseLogger(logLevel);
};

export default logger;
```

以上兩個code snippet以logger為例,說明了取得logger的資源調用方不用處理logLevel的變動所造成的影響,也不用知道現在的環境是debug還是release,這些變量都由instance提供方處理,以符合single responsibility principle單一責任原則.