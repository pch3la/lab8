## я всё ещё не научился прикреплять фотографии, поэтому прикреплю пнг в классруме

### 1)создал пустой проект 

### 2)добавил новый класс DBHelper

Вставил код 

public class DBHelper extends SQLiteOpenHelper
{
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "myBase";
    public static final String TABLE_PERSONS = "persons";
    public static final String KEY_ID = "_id";
    public static final String KEY_NAME = "name";
    public static final String KEY_MAIL = "mail";

    public DBHelper(@Nullable Context context)
    {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db)
    {
        db.execSQL("create table " + TABLE_PERSONS + "(" + KEY_ID + " integer primary key," + KEY_NAME + " text," + KEY_MAIL + " text" + ")");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
    {
        db.execSQL("drop table if exists " + TABLE_PERSONS);
        onCreate(db);
    }
}

### 3) В mainactivity 

public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    Button buttonAdd, buttonDelete, buttonClear, buttonRead, buttonUpdate;
    EditText etName, etEmail, etID;
    DBHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        buttonAdd = (Button) findViewById(R.id.buttonAdd);
        buttonAdd.setOnClickListener(this);

        buttonRead = (Button) findViewById(R.id.buttonRead);
        buttonRead.setOnClickListener(this);

        buttonClear = (Button) findViewById(R.id.buttonClear);
        buttonClear.setOnClickListener(this);

        buttonUpdate = (Button) findViewById(R.id.buttonUpdate);
        buttonUpdate.setOnClickListener(this);

        buttonDelete = (Button) findViewById(R.id.buttonDelete);
        buttonDelete.setOnClickListener(this);

        etID = (EditText) findViewById(R.id.editTextID);
        etName = (EditText) findViewById(R.id.editTextName);
        etEmail = (EditText) findViewById(R.id.editTextTextEmailAddress);

        dbHelper = new DBHelper(this);
    }

    public void onClick(View v)
    {
        String ID = etID.getText().toString();
        String name = etName.getText().toString();
        String email = etEmail.getText().toString();

        SQLiteDatabase database = dbHelper.getWritableDatabase();
        ContentValues contentValues = new ContentValues(); // класс для добавления новых строк в таблицу

        switch (v.getId())
        {
            case R.id.buttonAdd:
                contentValues.put(DBHelper.KEY_NAME, name);
                contentValues.put(DBHelper.KEY_MAIL, email);
                database.insert(DBHelper.TABLE_PERSONS, null, contentValues);
                break;

            case R.id.buttonRead:
                Cursor cursor = database.query(DBHelper.TABLE_PERSONS, null, null, null,
                        null, null, null); // все поля без сортировки и группировки

                if (((Cursor) cursor).moveToFirst())
                {
                    int idIndex = cursor.getColumnIndex(DBHelper.KEY_ID);
                    int nameIndex = cursor.getColumnIndex(DBHelper.KEY_NAME);
                    int emailIndex = cursor.getColumnIndex(DBHelper.KEY_MAIL);
                    do {
                        Log.d("mLog", "ID =" + cursor.getInt(idIndex) +
                                ", name = " + cursor.getString(nameIndex) +
                                ", email = " + cursor.getString(emailIndex));

                    } while (cursor.moveToNext());
                } else
                    Log.d("mLog", "0 rows");

                cursor.close(); // освобождение памяти
                break;

            case R.id.buttonClear:
                database.delete(DBHelper.TABLE_PERSONS, null, null);
                break;

            case R.id.buttonDelete:
                if (ID.equalsIgnoreCase(""))
                {
                    break;
                }
                int delCount = database.delete(DBHelper.TABLE_PERSONS, DBHelper.KEY_ID + "= " + ID, null);
                Log.d("mLog", "Удалено строк = " + delCount);

            case R.id.buttonUpdate:
                if (ID.equalsIgnoreCase(""))
                {
                    break;
                }
                contentValues.put(DBHelper.KEY_MAIL, email);
                contentValues.put(DBHelper.KEY_NAME, name);
                int updCount = database.update(DBHelper.TABLE_PERSONS, contentValues, DBHelper.KEY_ID + "= ?", new String[] {ID});
                Log.d("mLog", "Обновлено строк = " + updCount);
        }
        dbHelper.close(); // закрываем соединение с БД
    }
}

### 4) Проверка работоспособности  

pic1 в classroom 
