# 🛠️ Smart Pointers - Practical Examples
### "Real-world scenarios and best practices in action"

---

## 🎯 Introduction

This section covers practical, real-world examples of smart pointer usage patterns that you'll encounter in professional C++ development.

---

## 🏗️ Factory Pattern with Smart Pointers

### Abstract Factory Implementation

```cpp
// Abstract base class
class DatabaseConnection {
public:
    virtual ~DatabaseConnection() = default;
    virtual void connect() = 0;
    virtual void query(const std::string& sql) = 0;
    virtual void disconnect() = 0;
};

// Concrete implementations
class MySQLConnection : public DatabaseConnection {
public:
    void connect() override {
        std::cout << "Connecting to MySQL database" << std::endl;
    }
    
    void query(const std::string& sql) override {
        std::cout << "MySQL query: " << sql << std::endl;
    }
    
    void disconnect() override {
        std::cout << "Disconnecting from MySQL" << std::endl;
    }
};

class PostgreSQLConnection : public DatabaseConnection {
public:
    void connect() override {
        std::cout << "Connecting to PostgreSQL database" << std::endl;
    }
    
    void query(const std::string& sql) override {
        std::cout << "PostgreSQL query: " << sql << std::endl;
    }
    
    void disconnect() override {
        std::cout << "Disconnecting from PostgreSQL" << std::endl;
    }
};

// Factory class
enum class DatabaseType { MySQL, PostgreSQL };

class DatabaseConnectionFactory {
public:
    static unique_ptr<DatabaseConnection> create(DatabaseType type) {
        switch (type) {
            case DatabaseType::MySQL:
                return make_unique<MySQLConnection>();
            case DatabaseType::PostgreSQL:
                return make_unique<PostgreSQLConnection>();
            default:
                throw std::invalid_argument("Unsupported database type");
        }
    }
};

// Usage
void demonstrateFactory() {
    auto mysqlDb = DatabaseConnectionFactory::create(DatabaseType::MySQL);
    auto pgDb = DatabaseConnectionFactory::create(DatabaseType::PostgreSQL);
    
    mysqlDb->connect();
    mysqlDb->query("SELECT * FROM users");
    mysqlDb->disconnect();
    
    pgDb->connect();
    pgDb->query("SELECT * FROM products");
    pgDb->disconnect();
}
```

---

## 🎮 Game Object Management

### Entity Component System

```cpp
// Component base class
class Component {
public:
    virtual ~Component() = default;
    virtual void update(float deltaTime) = 0;
    virtual void render() = 0;
};

// Specific components
class TransformComponent : public Component {
private:
    float x_, y_, rotation_;
    
public:
    TransformComponent(float x, float y, float rotation) 
        : x_(x), y_(y), rotation_(rotation) {}
    
    void update(float deltaTime) override {
        // Update position based on velocity
        x_ += 10.0f * deltaTime;
    }
    
    void render() override {
        std::cout << "Rendering transform at (" << x_ << ", " << y_ << ")" << std::endl;
    }
    
    float getX() const { return x_; }
    float getY() const { return y_; }
};

class RenderComponent : public Component {
private:
    std::string textureName_;
    
public:
    RenderComponent(const std::string& texture) : textureName_(texture) {}
    
    void update(float deltaTime) override {
        // Animation updates
    }
    
    void render() override {
        std::cout << "Rendering texture: " << textureName_ << std::endl;
    }
};

// Game Entity
class GameObject {
private:
    std::vector<unique_ptr<Component>> components_;
    bool active_;
    
public:
    GameObject() : active_(true) {}
    
    template<typename T, typename... Args>
    T* addComponent(Args&&... args) {
        auto component = make_unique<T>(std::forward<Args>(args)...);
        T* ptr = component.get();
        components_.push_back(std::move(component));
        return ptr;
    }
    
    template<typename T>
    T* getComponent() {
        for (auto& component : components_) {
            if (auto casted = dynamic_cast<T*>(component.get())) {
                return casted;
            }
        }
        return nullptr;
    }
    
    void update(float deltaTime) {
        if (!active_) return;
        
        for (auto& component : components_) {
            component->update(deltaTime);
        }
    }
    
    void render() {
        if (!active_) return;
        
        for (auto& component : components_) {
            component->render();
        }
    }
    
    void setActive(bool active) { active_ = active; }
    bool isActive() const { return active_; }
};

// Game Manager
class GameManager {
private:
    std::vector<unique_ptr<GameObject>> gameObjects_;
    
public:
    GameObject* createGameObject() {
        auto obj = make_unique<GameObject>();
        GameObject* ptr = obj.get();
        gameObjects_.push_back(std::move(obj));
        return ptr;
    }
    
    void update(float deltaTime) {
        // Update all game objects
        for (auto& obj : gameObjects_) {
            obj->update(deltaTime);
        }
        
        // Remove inactive objects
        gameObjects_.erase(
            std::remove_if(gameObjects_.begin(), gameObjects_.end(),
                [](const unique_ptr<GameObject>& obj) {
                    return !obj->isActive();
                }),
            gameObjects_.end()
        );
    }
    
    void render() {
        for (auto& obj : gameObjects_) {
            obj->render();
        }
    }
};

// Usage
void demonstrateGameObjects() {
    GameManager gameManager;
    
    // Create player
    auto player = gameManager.createGameObject();
    auto playerTransform = player->addComponent<TransformComponent>(100.0f, 100.0f, 0.0f);
    auto playerRender = player->addComponent<RenderComponent>("player.png");
    
    // Create enemy
    auto enemy = gameManager.createGameObject();
    auto enemyTransform = enemy->addComponent<TransformComponent>(200.0f, 50.0f, 0.0f);
    auto enemyRender = enemy->addComponent<RenderComponent>("enemy.png");
    
    // Game loop simulation
    for (int frame = 0; frame < 5; frame++) {
        float deltaTime = 0.016f;  // 60 FPS
        std::cout << "\n=== Frame " << frame << " ===" << std::endl;
        
        gameManager.update(deltaTime);
        gameManager.render();
        
        // Deactivate enemy after 3 frames
        if (frame == 2) {
            enemy->setActive(false);
        }
    }
}
```

---

## 🌐 Web Server Connection Pool

### Connection Pool with shared_ptr

```cpp
class DatabaseConnection {
private:
    std::string connectionString_;
    bool inUse_;
    std::chrono::steady_clock::time_point lastUsed_;
    
public:
    DatabaseConnection(const std::string& connStr) 
        : connectionString_(connStr), inUse_(false) {
        std::cout << "Created database connection: " << connStr << std::endl;
    }
    
    ~DatabaseConnection() {
        std::cout << "Destroyed database connection: " << connectionString_ << std::endl;
    }
    
    bool acquire() {
        if (inUse_) return false;
        
        inUse_ = true;
        lastUsed_ = std::chrono::steady_clock::now();
        std::cout << "Acquired connection: " << connectionString_ << std::endl;
        return true;
    }
    
    void release() {
        inUse_ = false;
        std::cout << "Released connection: " << connectionString_ << std::endl;
    }
    
    void query(const std::string& sql) {
        if (!inUse_) {
            std::cout << "ERROR: Using connection without acquiring!" << std::endl;
            return;
        }
        std::cout << "Query on " << connectionString_ << ": " << sql << std::endl;
    }
    
    bool isInUse() const { return inUse_; }
    auto getLastUsed() const { return lastUsed_; }
};

class ConnectionPool {
private:
    std::vector<shared_ptr<DatabaseConnection>> connections_;
    std::mutex mutex_;
    std::chrono::seconds maxIdleTime_;
    
public:
    ConnectionPool(size_t poolSize, const std::string& baseConnStr, 
                   std::chrono::seconds maxIdleTime = std::chrono::seconds(300))
        : maxIdleTime_(maxIdleTime) {
        
        for (size_t i = 0; i < poolSize; ++i) {
            std::string connStr = baseConnStr + "_conn_" + std::to_string(i);
            connections_.push_back(make_shared<DatabaseConnection>(connStr));
        }
        
        std::cout << "Created connection pool with " << poolSize << " connections" << std::endl;
    }
    
    shared_ptr<DatabaseConnection> acquire() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        // Try to find an available connection
        for (auto& conn : connections_) {
            if (conn->acquire()) {
                return conn;  // Return shared_ptr that still owns the connection
            }
        }
        
        std::cout << "No available connections in pool!" << std::endl;
        return nullptr;
    }
    
    void cleanupIdleConnections() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        auto now = std::chrono::steady_clock::now();
        
        for (auto& conn : connections_) {
            if (!conn->isInUse()) {
                auto idleTime = now - conn->getLastUsed();
                if (idleTime > maxIdleTime_) {
                    std::cout << "Connection idle too long, would be cleaned up: " 
                             << std::chrono::duration_cast<std::chrono::seconds>(idleTime).count() 
                             << "s" << std::endl;
                }
            }
        }
    }
    
    size_t getAvailableCount() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        return std::count_if(connections_.begin(), connections_.end(),
            [](const shared_ptr<DatabaseConnection>& conn) {
                return !conn->isInUse();
            });
    }
};

// RAII Connection Holder
class ScopedConnection {
private:
    shared_ptr<DatabaseConnection> connection_;
    
public:
    explicit ScopedConnection(shared_ptr<DatabaseConnection> conn) 
        : connection_(conn) {}
    
    ~ScopedConnection() {
        if (connection_) {
            connection_->release();
        }
    }
    
    DatabaseConnection* operator->() { return connection_.get(); }
    DatabaseConnection& operator*() { return *connection_; }
    
    // Prevent copying
    ScopedConnection(const ScopedConnection&) = delete;
    ScopedConnection& operator=(const ScopedConnection&) = delete;
    
    // Allow moving
    ScopedConnection(ScopedConnection&& other) noexcept 
        : connection_(std::move(other.connection_)) {}
    
    ScopedConnection& operator=(ScopedConnection&& other) noexcept {
        if (this != &other) {
            if (connection_) {
                connection_->release();
            }
            connection_ = std::move(other.connection_);
        }
        return *this;
    }
};

// Usage
void demonstrateConnectionPool() {
    ConnectionPool pool(3, "mysql://localhost:3306/mydb");
    
    std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
    
    {
        // Acquire connection using RAII
        ScopedConnection conn1(pool.acquire());
        if (conn1) {
            conn1->query("SELECT * FROM users");
        }
        
        std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
        
        {
            ScopedConnection conn2(pool.acquire());
            if (conn2) {
                conn2->query("SELECT * FROM products");
            }
            
            std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
            
            ScopedConnection conn3(pool.acquire());
            if (conn3) {
                conn3->query("SELECT * FROM orders");
            }
            
            std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
        }  // conn2 released here
        
        std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
    }  // conn1 released here
    
    std::cout << "Available connections: " << pool.getAvailableCount() << std::endl;
    
    pool.cleanupIdleConnections();
}
```

---

## 📁 File System Manager

### PIMPL Pattern with unique_ptr

```cpp
// Forward declaration
class FileSystemManager;

// Implementation class (hidden from users)
class FileSystemManagerImpl {
private:
    std::string basePath_;
    std::unordered_map<std::string, FILE*> openFiles_;
    std::mutex fileMutex_;
    
public:
    FileSystemManagerImpl(const std::string& basePath) : basePath_(basePath) {
        std::cout << "FileSystemManagerImpl created with path: " << basePath << std::endl;
        
        // Ensure base directory exists
        #ifdef _WIN32
        _mkdir(basePath_.c_str());
        #else
        mkdir(basePath_.c_str(), 0755);
        #endif
    }
    
    ~FileSystemManagerImpl() {
        std::lock_guard<std::mutex> lock(fileMutex_);
        
        // Close all open files
        for (auto& [filename, file] : openFiles_) {
            if (file) {
                fclose(file);
                std::cout << "Closed file: " << filename << std::endl;
            }
        }
        
        std::cout << "FileSystemManagerImpl destroyed" << std::endl;
    }
    
    bool writeFile(const std::string& filename, const std::string& content) {
        std::lock_guard<std::mutex> lock(fileMutex_);
        
        std::string fullPath = basePath_ + "/" + filename;
        FILE* file = fopen(fullPath.c_str(), "w");
        
        if (!file) {
            std::cout << "Failed to open file for writing: " << filename << std::endl;
            return false;
        }
        
        size_t written = fwrite(content.c_str(), 1, content.length(), file);
        fclose(file);
        
        std::cout << "Wrote " << written << " bytes to file: " << filename << std::endl;
        return written == content.length();
    }
    
    std::string readFile(const std::string& filename) {
        std::lock_guard<std::mutex> lock(fileMutex_);
        
        std::string fullPath = basePath_ + "/" + filename;
        FILE* file = fopen(fullPath.c_str(), "r");
        
        if (!file) {
            std::cout << "Failed to open file for reading: " << filename << std::endl;
            return "";
        }
        
        // Get file size
        fseek(file, 0, SEEK_END);
        long size = ftell(file);
        fseek(file, 0, SEEK_SET);
        
        std::string content(size, '\0');
        size_t read = fread(&content[0], 1, size, file);
        fclose(file);
        
        std::cout << "Read " << read << " bytes from file: " << filename << std::endl;
        return content;
    }
    
    FILE* openFile(const std::string& filename, const std::string& mode) {
        std::lock_guard<std::mutex> lock(fileMutex_);
        
        auto it = openFiles_.find(filename);
        if (it != openFiles_.end() && it->second) {
            std::cout << "File already open: " << filename << std::endl;
            return it->second;
        }
        
        std::string fullPath = basePath_ + "/" + filename;
        FILE* file = fopen(fullPath.c_str(), mode.c_str());
        
        if (file) {
            openFiles_[filename] = file;
            std::cout << "Opened file: " << filename << std::endl;
        } else {
            std::cout << "Failed to open file: " << filename << std::endl;
        }
        
        return file;
    }
    
    void closeFile(const std::string& filename) {
        std::lock_guard<std::mutex> lock(fileMutex_);
        
        auto it = openFiles_.find(filename);
        if (it != openFiles_.end() && it->second) {
            fclose(it->second);
            openFiles_.erase(it);
            std::cout << "Closed file: " << filename << std::endl;
        }
    }
    
    size_t getOpenFileCount() const {
        std::lock_guard<std::mutex> lock(fileMutex_);
        return openFiles_.size();
    }
};

// Public interface class
class FileSystemManager {
private:
    unique_ptr<FileSystemManagerImpl> pimpl_;
    
public:
    explicit FileSystemManager(const std::string& basePath) 
        : pimpl_(make_unique<FileSystemManagerImpl>(basePath)) {}
    
    // Forwarding methods to implementation
    bool writeFile(const std::string& filename, const std::string& content) {
        return pimpl_->writeFile(filename, content);
    }
    
    std::string readFile(const std::string& filename) {
        return pimpl_->readFile(filename);
    }
    
    FILE* openFile(const std::string& filename, const std::string& mode) {
        return pimpl_->openFile(filename, mode);
    }
    
    void closeFile(const std::string& filename) {
        pimpl_->closeFile(filename);
    }
    
    size_t getOpenFileCount() const {
        return pimpl_->getOpenFileCount();
    }
};

// Usage
void demonstrateFileSystem() {
    FileSystemManager fsManager("./data");
    
    // Write some files
    fsManager.writeFile("config.txt", "debug=true\nport=8080");
    fsManager.writeFile("users.txt", "alice\nbob\ncharlie");
    
    // Read files
    std::string config = fsManager.readFile("config.txt");
    std::string users = fsManager.readFile("users.txt");
    
    std::cout << "Config content:\n" << config << std::endl;
    std::cout << "Users content:\n" << users << std::endl;
    
    // Open file for streaming operations
    FILE* logFile = fsManager.openFile("app.log", "a");
    if (logFile) {
        fprintf(logFile, "Application started at %ld\n", time(nullptr));
        fprintf(logFile, "Performing operations...\n");
        
        std::cout << "Open files count: " << fsManager.getOpenFileCount() << std::endl;
        
        fsManager.closeFile("app.log");
    }
    
    std::cout << "Final open files count: " << fsManager.getOpenFileCount() << std::endl;
}
```

---

## 🔄 Event System with weak_ptr

### Observer Pattern Implementation

```cpp
class Event {
public:
    virtual ~Event() = default;
    virtual std::string getType() const = 0;
    virtual std::string getDescription() const = 0;
};

class PlayerDeathEvent : public Event {
private:
    std::string playerName_;
    
public:
    explicit PlayerDeathEvent(const std::string& name) : playerName_(name) {}
    
    std::string getType() const override { return "PlayerDeath"; }
    std::string getDescription() const override {
        return "Player " + playerName_ + " has died";
    }
    
    const std::string& getPlayerName() const { return playerName_; }
};

class LevelUpEvent : public Event {
private:
    std::string playerName_;
    int newLevel_;
    
public:
    LevelUpEvent(const std::string& name, int level) 
        : playerName_(name), newLevel_(level) {}
    
    std::string getType() const override { return "LevelUp"; }
    std::string getDescription() const override {
        return playerName_ + " reached level " + std::to_string(newLevel_);
    }
    
    const std::string& getPlayerName() const { return playerName_; }
    int getNewLevel() const { return newLevel_; }
};

// Observer interface
class EventObserver {
public:
    virtual ~EventObserver() = default;
    virtual void onEvent(const Event& event) = 0;
};

// Event system
class EventSystem {
private:
    std::unordered_map<std::string, std::vector<weak_ptr<EventObserver>>> observers_;
    std::queue<unique_ptr<Event>> eventQueue_;
    std::mutex mutex_;
    
public:
    void subscribe(const std::string& eventType, shared_ptr<EventObserver> observer) {
        std::lock_guard<std::mutex> lock(mutex_);
        observers_[eventType].push_back(observer);
        std::cout << "Subscribed observer to " << eventType << std::endl;
    }
    
    void unsubscribe(const std::string& eventType, shared_ptr<EventObserver> observer) {
        std::lock_guard<std::mutex> lock(mutex_);
        
        auto& obsList = observers_[eventType];
        obsList.erase(
            std::remove_if(obsList.begin(), obsList.end(),
                [&observer](const weak_ptr<EventObserver>& weakObs) {
                    return weakObs.lock() == observer;
                }),
            obsList.end()
        );
        
        std::cout << "Unsubscribed observer from " << eventType << std::endl;
    }
    
    void publishEvent(unique_ptr<Event> event) {
        std::lock_guard<std::mutex> lock(mutex_);
        eventQueue_.push(std::move(event));
        std::cout << "Queued event: " << eventQueue_.back()->getDescription() << std::endl;
    }
    
    void processEvents() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        while (!eventQueue_.empty()) {
            auto event = std::move(eventQueue_.front());
            eventQueue_.pop();
            
            std::string eventType = event->getType();
            auto& observers = observers_[eventType];
            
            // Remove expired observers and notify active ones
            observers.erase(
                std::remove_if(observers.begin(), observers.end(),
                    [&event](const weak_ptr<EventObserver>& weakObs) {
                        if (auto observer = weakObs.lock()) {
                            observer->onEvent(*event);
                            return false;  // Keep this observer
                        }
                        return true;   // Remove expired observer
                    }),
                observers.end()
            );
        }
    }
    
    size_t getObserverCount(const std::string& eventType) {
        std::lock_guard<std::mutex> lock(mutex_);
        
        auto it = observers_.find(eventType);
        if (it == observers_.end()) return 0;
        
        return std::count_if(it->second.begin(), it->second.end(),
            [](const weak_ptr<EventObserver>& weakObs) {
                return !weakObs.expired();
            });
    }
};

// Concrete observers
class AchievementSystem : public EventObserver, public enable_shared_from_this<AchievementSystem> {
private:
    std::unordered_map<std::string, int> deathCounts_;
    
public:
    AchievementSystem() {
        std::cout << "AchievementSystem created" << std::endl;
    }
    
    void onEvent(const Event& event) override {
        if (event.getType() == "PlayerDeath") {
            const auto& deathEvent = static_cast<const PlayerDeathEvent&>(event);
            deathCounts_[deathEvent.getPlayerName()]++;
            
            std::cout << "AchievementSystem: " << deathEvent.getPlayerName() 
                     << " has died " << deathCounts_[deathEvent.getPlayerName()] 
                     << " times" << std::endl;
            
            // Check for achievements
            if (deathCounts_[deathEvent.getPlayerName()] == 10) {
                std::cout << "Achievement unlocked: Die Hard (10 deaths)" << std::endl;
            }
        }
    }
    
    void subscribeToEvents(shared_ptr<EventSystem> eventSystem) {
        eventSystem->subscribe("PlayerDeath", shared_from_this());
        eventSystem->subscribe("LevelUp", shared_from_this());
    }
    
    ~AchievementSystem() {
        std::cout << "AchievementSystem destroyed" << std::endl;
    }
};

class Logger : public EventObserver, public enable_shared_from_this<Logger> {
private:
    std::string logFile_;
    
public:
    explicit Logger(const std::string& filename) : logFile_(filename) {
        std::cout << "Logger created for " << filename << std::endl;
    }
    
    void onEvent(const Event& event) override {
        std::cout << "Logger: " << event.getDescription() << std::endl;
        // In real implementation, would write to log file
    }
    
    void subscribeToEvents(shared_ptr<EventSystem> eventSystem) {
        eventSystem->subscribe("PlayerDeath", shared_from_this());
        eventSystem->subscribe("LevelUp", shared_from_this());
    }
    
    ~Logger() {
        std::cout << "Logger destroyed" << std::endl;
    }
};

// Usage
void demonstrateEventSystem() {
    auto eventSystem = make_shared<EventSystem>();
    
    // Create observers
    auto achievements = make_shared<AchievementSystem>();
    auto logger = make_shared<Logger>("game.log");
    
    // Subscribe to events
    achievements->subscribeToEvents(eventSystem);
    logger->subscribeToEvents(eventSystem);
    
    std::cout << "PlayerDeath observers: " << eventSystem->getObserverCount("PlayerDeath") << std::endl;
    std::cout << "LevelUp observers: " << eventSystem->getObserverCount("LevelUp") << std::endl;
    
    // Publish some events
    eventSystem->publishEvent(make_unique<PlayerDeathEvent>("Alice"));
    eventSystem->publishEvent(make_unique<LevelUpEvent>("Bob", 5));
    eventSystem->publishEvent(make_unique<PlayerDeathEvent>("Alice"));
    
    // Process events
    eventSystem->processEvents();
    
    // Destroy one observer
    std::cout << "Destroying logger..." << std::endl;
    logger.reset();
    
    std::cout << "PlayerDeath observers: " << eventSystem->getObserverCount("PlayerDeath") << std::endl;
    
    // Publish more events
    eventSystem->publishEvent(make_unique<PlayerDeathEvent>("Alice"));
    eventSystem->processEvents();
}
```

---

## 🎯 Performance Monitoring

### Resource Tracker with weak_ptr

```cpp
class Resource {
private:
    std::string name_;
    size_t size_;
    std::chrono::steady_clock::time_point created_;
    
public:
    Resource(const std::string& name, size_t size) 
        : name_(name), size_(size), created_(std::chrono::steady_clock::now()) {
        std::cout << "Resource created: " << name_ << " (" << size_ << " bytes)" << std::endl;
    }
    
    ~Resource() {
        auto lifetime = std::chrono::steady_clock::now() - created_;
        std::cout << "Resource destroyed: " << name_ 
                 << " (lived " << std::chrono::duration_cast<std::chrono::milliseconds>(lifetime).count() 
                 << "ms)" << std::endl;
    }
    
    const std::string& getName() const { return name_; }
    size_t getSize() const { return size_; }
    auto getCreated() const { return created_; }
};

class ResourceTracker {
private:
    std::unordered_map<std::string, weak_ptr<Resource>> resources_;
    std::mutex mutex_;
    
public:
    void trackResource(const std::string& name, shared_ptr<Resource> resource) {
        std::lock_guard<std::mutex> lock(mutex_);
        resources_[name] = resource;
        std::cout << "Tracking resource: " << name << std::endl;
    }
    
    void stopTracking(const std::string& name) {
        std::lock_guard<std::mutex> lock(mutex_);
        resources_.erase(name);
        std::cout << "Stopped tracking: " << name << std::endl;
    }
    
    void printStatus() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        std::cout << "\n=== Resource Status ===" << std::endl;
        std::cout << "Total tracked: " << resources_.size() << std::endl;
        
        size_t totalSize = 0;
        size_t aliveCount = 0;
        
        for (const auto& [name, weakRes] : resources_) {
            if (auto res = weakRes.lock()) {
                aliveCount++;
                totalSize += res->getSize();
                auto age = std::chrono::steady_clock::now() - res->getCreated();
                std::cout << "  ✓ " << name << " (" << res->getSize() << " bytes, "
                         << std::chrono::duration_cast<std::chrono::milliseconds>(age).count() 
                         << "ms old)" << std::endl;
            } else {
                std::cout << "  ✗ " << name << " (destroyed)" << std::endl;
            }
        }
        
        std::cout << "Alive resources: " << aliveCount << std::endl;
        std::cout << "Total memory: " << totalSize << " bytes" << std::endl;
        std::cout << "=====================" << std::endl;
    }
    
    void cleanupDestroyed() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        for (auto it = resources_.begin(); it != resources_.end();) {
            if (it->second.expired()) {
                std::cout << "Cleaning up destroyed resource: " << it->first << std::endl;
                it = resources_.erase(it);
            } else {
                ++it;
            }
        }
    }
};

// Usage
void demonstrateResourceTracking() {
    ResourceTracker tracker;
    
    // Create some resources
    auto res1 = make_shared<Resource>("texture_player.png", 1024 * 1024);
    auto res2 = make_shared<Resource>("sound_background.ogg", 5 * 1024 * 1024);
    auto res3 = make_shared<Resource>("level_data.json", 2048);
    
    // Track them
    tracker.trackResource("texture_player.png", res1);
    tracker.trackResource("sound_background.ogg", res2);
    tracker.trackResource("level_data.json", res3);
    
    tracker.printStatus();
    
    // Destroy one resource
    res2.reset();
    
    std::cout << "\nAfter destroying sound_background.ogg:" << std::endl;
    tracker.printStatus();
    
    // Clean up destroyed references
    tracker.cleanupDestroyed();
    tracker.printStatus();
}
```

---

## 🎓 Key Takeaways

1. **Factory Pattern**: Use `unique_ptr` for object creation and ownership transfer
2. **Game Systems**: Component-based architecture with `unique_ptr` for automatic cleanup
3. **Connection Pools**: `shared_ptr` for resource sharing with RAII wrappers
4. **PIMPL Pattern**: Hide implementation details with `unique_ptr`
5. **Event Systems**: `weak_ptr` for observer patterns without circular references
6. **Resource Tracking**: `weak_ptr` to monitor resource lifetimes without ownership

### Best Practices Summary

| Pattern | Smart Pointer | Reason |
|---------|---------------|--------|
| Factory | `unique_ptr` | Clear ownership transfer |
| Component Systems | `unique_ptr` | Automatic cleanup, no sharing |
| Connection Pools | `shared_ptr` | Multiple users, RAII management |
| PIMPL | `unique_ptr` | Hide implementation, automatic cleanup |
| Observer Pattern | `weak_ptr` | Prevent circular references |
| Resource Monitoring | `weak_ptr` | Observe without affecting lifetime |

These patterns represent real-world scenarios you'll encounter in professional C++ development. Master them, and you'll write safer, more maintainable code! 🚀
