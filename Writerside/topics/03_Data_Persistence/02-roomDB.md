# Room Database

Firstly create a Entity class, which will be a table in the database.

```Java
@Entity(tableName = "note_table")
public class Note {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private String title;
    private String text;
    private int priority;

    public Note(String title, String text, int priority) {
        this.title = title;
        this.text = text;
        this.priority = priority;
    }
```

Then create a Dao interface, which will be used to access the database.

```Java
@Dao
public interface NoteDao {
    @Query("SELECT * FROM note_table ORDER BY priority DESC")
    LiveData<List<Note>> getAllNotes();

    @Insert
    void insert(Note note);
    @Update
    void update(Note note);
    @Delete
    void delete(Note note);
    @Query("DELETE FROM note_table")
    void deleteAll();
}
```

Now, create a Database class, which will be used to create the database.

```Java
@Database(entities = {Note.class}, version = 1)
public abstract class NoteDatabase extends RoomDatabase {
    public static NoteDatabase instance = null;

    public abstract NoteDao noteDao();

    public static synchronized NoteDatabase getInstance(Context context) {
        if (instance == null) {
            instance = Room.databaseBuilder(context.getApplicationContext(), NoteDatabase.class, "note_database")
                    .fallbackToDestructiveMigration()
                    .addCallback(roomCallback)
                    .build();
        }
        return instance;
    }
```

We can add a **Callback** to Populate the database with some data when it is created.

```Java
private static RoomDatabase.Callback roomCallback = new RoomDatabase.Callback(){
    @Override
    public void onCreate(@NonNull SupportSQLiteDatabase db) {
        super.onCreate(db);
        // To Populate the Database as soon as it will be created
        PopulateAsyncTask populateAsyncTask = new PopulateAsyncTask(instance);
        populateAsyncTask.execute();
    }
};

private static class PopulateAsyncTask extends AsyncTask<Void, Void, Void> {
    private NoteDao noteDao;
    public PopulateAsyncTask(NoteDatabase instance) {
        // onCreate() will be created after the database is created
        // therefore we can use NoteDatabase instance
        noteDao = instance.noteDao();
    }
    @Override
    protected Void doInBackground(Void... voids) {
        noteDao.insert(new Note("Title 1", "Description 1", 1));
        noteDao.insert(new Note("Title 2", "Description 2", 2));
        noteDao.insert(new Note("Title 3", "Description 3", 3));
        return null;
    }
}
```
