# Room Database
<show-structure depth="2"/>

The Room persistence library provides an abstraction layer 
over SQLite to allow fluent database access while harnessing the full power of SQLite. 

In particular, Room provides the following benefits:
- Compile-time verification of SQL queries.
- Convenience annotations that minimize repetitive and error-prone boilerplate code.
- Streamlined database migration paths.

## Basic Setup

```kotlin
@Entity(tableName = "contact")
data class Contact(

    @PrimaryKey(autoGenerate = true)
    val id: Long,

    val name: String,

    val phone: String,
)
```

```kotlin
@Dao
interface ContactDao {

    @Insert
    suspend fun insertContact(contact: Contact)

    @Update
    suspend fun updateContact(contact: Contact)

    @Delete
    suspend fun deleteContact(contact: Contact)

    @Query("SELECT * FROM contact")
    suspend fun getContacts(): List<Contact>
}
```

```kotlin
@Database(entities = [Contact::class], version = 1)
abstract class MyDatabase: RoomDatabase() {
    abstract fun contactDao(): ContactDao
}
```

```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var database: MyDatabase

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // create database instance
        database = Room.databaseBuilder<MyDatabase>(applicationContext, "MyDatabase")
            .setDriver(AndroidSQLiteDriver())
            .build()
        // Note: this is not the correct way to create database instance, it is only for simple understanding

        GlobalScope.launch {
            // insert contact record
            database.contactDao().insertContact(Contact(id = 0, name = "John", phone = "1111111111"))
        }

        GlobalScope.launch {
            // log all contacts
            val contacts = database.contactDao().getContacts()
            Log.d(TAG, "logAllContacts: $contacts")
        }
    }
}
```

## Singleton DB instance

```kotlin
@Database(entities = [Contact::class], version = 1)
abstract class MyDatabase: RoomDatabase() {
    abstract fun contactDao(): ContactDao

    companion object {
        @Volatile
        private var INSTANCE: MyDatabase? = null

        fun getInstance(context: Context): MyDatabase {
            return INSTANCE ?:
            synchronized(this) {
                INSTANCE ?:
                Room.databaseBuilder<MyDatabase>(context.applicationContext, "MyDatabase")
                    .setDriver(AndroidSQLiteDriver())
                    .build().also { INSTANCE = it }
            }
        }
    }
}

// directly use singleton DB instance from activity etc.
val database = MyDatabase.getInstance(this)
```

> The second check inside synchronized is important: `INSTANCE ?: Room.databaseBuilder(...)`
>
> Without that, both threads could potentially create an instance.

## Type Converters

SQLite only supports:
- NULL
- INTEGER
- REAL
- TEXT
- BLOB

Sometimes, you need your app to store a custom data type in a single database column. 
You support custom types by providing type converters. 

These are functions that tell Room how to convert custom types to and from known types that Room can persist.

```kotlin
@Entity(tableName = "contact")
data class Contact(

    @PrimaryKey(autoGenerate = true)
    val id: Long,

    val name: String,

    val phone: String,

    val lastCall: Date, // We want to store Date in DB
)
```

We need to implement, how to convert a `Date` into `REAL` type, and a `REAL` into `Date` type.

```kotlin
object Converters {

    @ColumnTypeConverter
    fun fromTimestamp(value: Long?): Date? {
        return value?.let { Date(it) }
    }

    @ColumnTypeConverter
    fun dateToTimestamp(date: Date?): Long? {
        return date?.time
    }
}
```

Finally, we has to assign converter to the DB class.

```kotlin
@Database(entities = [Contact::class], version = 1)
@ColumnTypeConverters(Converters::class) // here
abstract class MyDatabase: RoomDatabase() {
    abstract fun contactDao(): ContactDao
}
```

## Version Migration

### Auto Migration

Auto Migration happens based on a schema `json` file.

It needs to have schema of older version to compare with newer version and find the differences.

> Define **room3** plugin in **libs.versions.toml**
> 
> ```toml
> [versions]
> # ...
> room3 = "3.0.2"
> 
> [plugins]
> # ...
> room = { id = "androidx.room3", version.ref = "room3" }
> ```
>
> Add **room3** plugin & **room3** extension to app module **build.gradle**
> 
> ```kotlin
> plugins {
>     // ...
>     alias(libs.plugins.room)
> }
> 
> android {
>     // targetSdk = 37 ...
> }
> 
> room3 {
>     schemaDirectory("$projectDir/schemas")
> }
> 
> dependencies {
>     // ...
> }
> ```

> `json` files live in `\projectDirectory\app\schemas\`

#### For a new project
- set `exportSchema` to `true`

```kotlin
@Database(
    version = 1,
    entities = [Contact::class],
    exportSchema = true
)
abstract class MyDatabase: RoomDatabase() {
    abstract fun contactDao(): ContactDao
}
```

#### For existing project:
- set `exportSchema` to `true`
- configure **room3** extension in **build.gradle** file (this will generate schema for existing version)
- write code for new version (change `Entities` etc.), update database version number, 
  and include `autoMigrations` from older version to this new version

```kotlin
@Database(
    version = 3,
    entities = [
        Contact::class,
    ],
    autoMigrations = [
        AutoMigration(from = 2, to = 3),
    ],
    exportSchema = true
)
abstract class MyDatabase: RoomDatabase() {
    abstract fun contactDao(): ContactDao
}
```
