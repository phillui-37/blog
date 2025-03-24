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

> 20250324: 抱歉之前的代碼是錯誤的，現已修正

## 工廠模式 - Factory Method Pattern

用途: 將建立實體的邏輯放於其規格內部,以達到封裝及資源管理的目的.

例子:

```Java
abstract class Logger {
    protected int logLevel;
    
    Logger(int logLevel) {
        this.logLevel = logLevel;
    }

    // This is the factory method that subclasses must implement
    protected abstract Logger createLogger(int logLevel);

    // The template method that uses the factory method
    static Logger getInstance(int logLevel) {
        LoggerFactory factory = new LoggerFactory();
        return factory.createLogger(logLevel);
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

// Factory class that decides which logger to create
class LoggerFactory {
    protected Logger createLogger(int logLevel) {
        if (env == "DEBUG") {
            return new DebugLogger(logLevel);
        }
        return new ReleaseLogger(logLevel);
    }
}

// Or even better, separate factories for each type
class DebugLoggerFactory extends LoggerFactory {
    @Override
    protected Logger createLogger(int logLevel) {
        return new DebugLogger(logLevel);
    }
}

class ReleaseLoggerFactory extends LoggerFactory {
    @Override
    protected Logger createLogger(int logLevel) {
        return new ReleaseLogger(logLevel);
    }
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

// Abstract factory (using closure)
const createLoggerFactory = () => {
    // This is the abstract factory method that concrete factories must implement
    const createLogger = (logLevel) => {
        throw new Error('Concrete factories must implement createLogger');
    };

    return {
        createLogger
    };
};

// Concrete factory for debug logging
const createDebugLoggerFactory = () => {
    const loggerInstance = AVAILABLE_LOG_LEVEL.reduce((acc, level) => {
        return {...acc, [level]: (msg) => console[level](msg)};
    }, {});

    const createLogger = (logLevel) => {
        let currentLogLevel = logLevel ?? DEFAULT_LOG_LEVEL;
        return {
            ...loggerInstance,
            setLogLevel: (_logLevel) => {currentLogLevel = _logLevel},
            getLogLevel: () => currentLogLevel,
        };
    };

    return {
        createLogger
    };
};

// Concrete factory for release logging
const createReleaseLoggerFactory = () => {
    const createLogger = (logLevel) => {
        let currentLogLevel = logLevel ?? DEFAULT_LOG_LEVEL;
        
        const logFnMeta = (level) => {
            if (currentLogLevel <= level)
                return (msg) => console[level](msg);
            else
                return (_) => {};
        };

        const loggerInstance = AVAILABLE_LOG_LEVEL.reduce((acc, level) => {
            return {...acc, [level]: logFnMeta(level)};
        }, {});

        return {
            ...loggerInstance,
            setLogLevel: (_logLevel) => {
                currentLogLevel = _logLevel;
                // Recreate logger instance with new log level
                Object.assign(loggerInstance, AVAILABLE_LOG_LEVEL.reduce((acc, level) => {
                    return {...acc, [level]: logFnMeta(level)};
                }, {}));
            },
            getLogLevel: () => currentLogLevel,
        };
    };

    return {
        createLogger
    };
};

// Factory selector
const logger = (logLevel) => {
    const factory = env === 'DEBUG' 
        ? createDebugLoggerFactory() 
        : createReleaseLoggerFactory();
    
    return factory.createLogger(logLevel);
};

export default logger;
```

以上兩個code snippet以logger為例,說明了取得logger的資源調用方不用處理logLevel的變動所造成的影響,也不用知道現在的環境是debug還是release,這些變量都由instance提供方處理,以符合single responsibility principle單一責任原則.