theme: Plain jane, 3
autoscale: true

# Recycler View

---

## Why Recycler View

- Before Lollipop: ListView, GridView
- Doing Holder pattern manually
- Changing from grid <--> list not easy

---

## Adding Recycler View to Gradle

- Part of support library
- We don't need to import the full support library

```
    compile 'com.android.support:recyclerview-v7:23.1.1'

```

---

## Parts of the puzzle

- A fragment containing the RecyclerView
- An adapter to create/recycle rows in the RecyclerView and to set its data.
    - we treat Click events inside the Adapter
- An AdapterView to hold references to the widgets / let us reach that controls in the row
- Some data in the form of a model object

---

## Parts of the puzzle

1. in the Fragment we create / obtain some model objects (or they are injected from the Activity)
1. in the Fragment we obtain a reference to the RecyclerView
    - we need to set the LayoutManager of the RecyclerView
1. we create the Adapter, pass the adapter to the RecyclerView
1. the RecyclerView asks the Adapter how many objects are there
1. the RecyclerView creates some Views (rows) to show on screen. Just the ones needed. Those views are ViewHolders
1. the RecyclerView "recycles" those views

---

## Model: A note. just a simple JavaBean class

```java
public class Note {
    private String title;
    private String text;

    public Note(String title, String text) {
        this.title = title;
        this.text = text;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getText() {
        return text;
    }

    public void setText(String text) {
        this.text = text;
    }
}
```

---


## ViewHolder: Used to display Model objects

```java

import android.support.v7.widget.RecyclerView;

public class NotesListRowAdapter extends RecyclerView.ViewHolder {

    private String title;
    private String text;

    private TextView titleTextView;
    private TextView textTextView;

    public NotesListRowAdapter(View rowNote) {
        super(rowNote);

        titleTextView = (TextView)rowNote.findViewById(R.id.row_list_notes_title_text);
        textTextView = (TextView)rowNote.findViewById(R.id.row_list_notes_title_note);
    }

    public void setTitle(String title) {
        this.titleTextView.setText(title);
    }

    public void setText(String text) {
        this.textTextView.setText(text);
    }
}
```

---

## Adapter

```java
public class NotesAdapter extends RecyclerView.Adapter<NotesListRowAdapter> {
    private Notes notes;
    private LayoutInflater layoutInflater;
    
    // Listener interface
    
    public interface OnElementClick<Note> {
        public void clickedOn(Note note, int position);
    }

    private OnElementClick<Notebook> listener;

    public NotesAdapter(Notes notes, Context context) {
        this.notes = notes;
        layoutInflater = LayoutInflater.from(context);
    }

    @Override
    public int getItemCount() {
        return notes.count();
    }
    
    public void setOnElementClickListener(@NonNull final OnElementClick<Note> listener) {
        this.listener = listener;
    }
}
```

---

## Adapter

```java

    @Override
    public NotesListRowAdapter onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = layoutInflater.inflate(R.layout.row_list_notes, parent, false);

        return new NotesListRowAdapter(view);
    }

    @Override
    public void onBindViewHolder(NotesListRowAdapter holder, int position) {
        Note note = notes.get(position);

        holder.setText(note.getText());
        holder.setTitle(note.getTitle());
        
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (listener != null) {
                    listener.clickedOn(note, position);
                }
            }
        });
    }
}

```

---

## Fragment

```java
public class NotesFragment extends Fragment {
    private RecyclerView notesRecyclerView;
    private NotesAdapter adapter;

    public NotesFragment() {
        // Required empty public constructor
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_notes, container, false);

        notesRecyclerView = (RecyclerView) view.findViewById(R.id.notes_recycler_view);
        notesRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));

        updateUI();

        return view;
    }

    private void updateUI() {
        Notes notes = new Notes();

        adapter = new NotesAdapter(notes, getActivity());
        notesRecyclerView.setAdapter(adapter);
    }
}

```
