# MapManager
MapManager is a comprehensive arena management system for Minecraft Bukkit/Spigot servers. It provides a robust framework for creating, managing, and customizing game arenas in separate worlds.
## Core Features
- Dynamic world creation dedicated to arenas
- Schematic management system for placing predefined structures
- Custom spawn points support
- Flexible configuration for individual arenas
- Comprehensive API for integration with other plugins
- Event system for arena interaction

## Installation
1. Download the latest JAR file from the Releases section
2. Place the file in your server's `plugins` folder
3. Restart the server or load the plugin with a plugin manager

## Requirements
- Minecraft Bukkit/Spigot/Paper server
- Java 17 or higher
- WorldEdit (for schematic functionality)

## API Reference
### Getting Started
``` java
import dev.thepranker.mapManager.application.api.MapManagerAPI;

// Access the API
MapManagerAPI api = MapManagerAPI.get();
```
### Arena Management
#### Creating Arenas
``` java
// Create arena with default schematic and global configuration
UUID arenaId = api.createArena("default_arena");

// Create arena with custom configuration
ArenaConfig config = new ArenaConfig()
    .withMaxPlayersPerArena(8)
    .withAutoStartArenas(true);
UUID customArenaId = api.createArena("custom_schema", config);
```
#### Managing Arena Lifecycle
``` java
// Start an arena
api.startArena(arenaId);

// End an arena (removes from world)
api.endArena(arenaId);

// Check if arena is full
boolean isFull = api.isArenaFull(arenaId);

// Get arena configuration
ArenaConfig config = api.getArenaConfig(arenaId);

// Get all active arenas
List<UUID> activeArenas = api.getActiveArenas();

// Get the world containing an arena
UUID worldId = api.getWorldForArena(arenaId);
```
#### Player Management
``` java
// Add player to arena
api.addPlayerToArena(arenaId, player);

// Remove player from arena
api.removePlayerFromArena(arenaId, player);

// Teleport player to specific spawn point
api.teleportPlayerToArenaSpawn(arenaId, player, spawnIndex);
```
### Schematic System
#### Managing Schematics
``` java
// Register new schematic with custom spawn points
List<SchematicData.RelativeLocation> spawnPoints = new ArrayList<>();
spawnPoints.add(new SchematicData.RelativeLocation(10, 65, 10));
spawnPoints.add(new SchematicData.RelativeLocation(20, 65, 20));
api.registerSchematic("custom_arena", spawnPoints);

// Add spawn point to existing schematic
api.addSpawnLocationToSchematic("custom_arena", 30, 65, 30);

// Get all available schematics
List<String> schematics = api.getAvailableSchematics();
```
### Configuration
#### Global Configuration
``` java
// Update global configuration
ArenaConfig globalConfig = new ArenaConfig()
    .withMaxArenasPerWorld(5)
    .withMinDistanceBetweenArenas(200)
    .withRemoveWorldWhenEmpty(true)
    .withAutoStartArenas(true)
    .withMinPlayersToStart(4)
    .withMaxPlayersPerArena(16);
api.updateGlobalConfig(globalConfig);

// Enable debug mode
api.setDebugMode(true);
```
#### Arena Configuration Options

| Configuration Option | Description | Default |
| --- | --- | --- |
| maxArenasPerWorld | Maximum number of arenas per world | 10 |
| minDistanceBetweenArenas | Minimum distance between arenas | 100 |
| removeWorldWhenEmpty | Remove world when no arenas are active | true |
| autoStartArenas | Automatically start arena when minimum players reached | false |
| minPlayersToStart | Minimum players required to start arena | 2 |
| maxPlayersPerArena | Maximum players allowed per arena | 10 |
### Event System
MapManager provides several events that can be used to interact with arenas:
#### Available Events

| Event | Description |
| --- | --- |
| ArenaCreatedEvent | Triggered when a new arena is created |
| ArenaEndedEvent | Triggered when an arena ends |
| WorldCreatedEvent | Triggered when a new world is created |
| WorldRemovedEvent | Triggered when a world is unloaded |
#### Event Listening Example
``` java
@EventHandler
public void onArenaCreated(ArenaCreatedEvent event) {
    Arena arena = event.getArena();
    ArenaWorld world = event.getWorld();
    
    // Custom logic...
}

@EventHandler
public void onArenaEnded(ArenaEndedEvent event) {
    Arena arena = event.getArena();
    ArenaWorld world = event.getWorld();
    
    // Custom logic...
}
```
### Advanced Usage
#### Direct Access to Managers
``` java
// Access WorldManager directly
WorldManager worldManager = api.getWorldManager();

// Access SchematicManager directly
SchematicManager schematicManager = api.getSchematicManager();
```
#### Creating Custom Arena Configurations
``` java
ArenaConfig config = new ArenaConfig()
    // Maximum arenas per world
    .withMaxArenasPerWorld(5)
    
    // Distance between arenas
    .withMinDistanceBetweenArenas(200)
    
    // Remove world when empty
    .withRemoveWorldWhenEmpty(true)
    
    // Auto-start arena when player threshold reached
    .withAutoStartArenas(true)
    
    // Minimum players to start arena
    .withMinPlayersToStart(4)
    
    // Maximum players per arena
    .withMaxPlayersPerArena(16);
```
#### Custom Schematic Spawn Points
``` java
// Create relative location with yaw and pitch
SchematicData.RelativeLocation spawn = new SchematicData.RelativeLocation(
    10,    // X coordinate
    65,    // Y coordinate
    10,    // Z coordinate
    90,    // Yaw (rotation)
    0      // Pitch
);

// Register a schematic with custom spawn points
List<SchematicData.RelativeLocation> spawnPoints = new ArrayList<>();
spawnPoints.add(spawn);
api.registerSchematic("custom_arena", spawnPoints);
```
## Internal Architecture
### Core Components
- **MapManagerAPI**: Main entry point for interacting with the plugin
- **WorldManager**: Handles creation and management of worlds
- **SchematicManager**: Manages schematics and their placement
- : Represents a game arena with players and spawn locations **Arena**
- : Contains multiple arenas in a single Bukkit world **ArenaWorld**
- : Stores information about a schematic and its spawn points **SchematicData**
- : Configuration settings for arenas **ArenaConfig**

### Design Patterns
- **Singleton**: Managers are accessed via static get() methods
- **Immutable Objects**: Domain objects use with() methods for updates
- **Event-driven**: System relies on Bukkit events for communication

## File Structure
- `/schematics/`: Directory for storing schematic files (.schem)
- : Plugin metadata and commands `plugin.yml`
- `config.yml`: Global configuration settings

## Extending MapManager
### Creating Custom Arena Types
Extend the Arena class to create custom arena types with specialized behavior:
``` java
public class MyCustomArena extends Arena {
    // Additional properties
    private final int customProperty;
    
    public MyCustomArena(String schematicName, Location origin, 
                         List<Location> spawnLocations, ArenaConfig config, 
                         int customProperty) {
        super(schematicName, origin, spawnLocations, config);
        this.customProperty = customProperty;
    }
    
    // Custom methods
    public void customMethod() {
        // Custom logic
    }
}
```
### Integration with Other Plugins
``` java
// Example: Integration with a game plugin
public class GameIntegration {
    private final MapManagerAPI mapManager;
    private final GamePlugin gamePlugin;
    
    public GameIntegration(GamePlugin gamePlugin) {
        this.mapManager = MapManagerAPI.get();
        this.gamePlugin = gamePlugin;
    }
    
    public void startGame(String mapName, int playerCount) {
        // Create arena with game-specific configuration
        ArenaConfig config = new ArenaConfig()
            .withMaxPlayersPerArena(playerCount)
            .withAutoStartArenas(true);
            
        UUID arenaId = mapManager.createArena(mapName, config);
        
        // Register game with the arena
        gamePlugin.registerGame(arenaId);
    }
}
```
## Troubleshooting
### Common Issues
1. **Schematics not loading**: Ensure schematics are in the correct format (.schem) and placed in the plugin's schematics folder
2. **Worlds not unloading**: Check for players or entities still in the world
3. **Performance issues**: Try reducing maxArenasPerWorld or increasing minDistanceBetweenArenas

### Debugging
Enable debug mode to see detailed logs:
``` java
api.setDebugMode(true);
```
## Contributing
Contributions are welcome! Please open an issue or submit a pull request on GitHub.
## License
This project is licensed under the MIT License - see the LICENSE file for details.
