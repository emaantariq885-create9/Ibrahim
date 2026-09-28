# Supabase and Next.js Database Connection

## 1. Introduction

I created a Note App using **Next.js** and **Supabase**.

Supabase is used as the database where my notes/information are stored. Next.js is used to create the form and display the saved information.

The main steps are:

1. Create a Supabase project.
2. Create a database table.
3. Connect Supabase with Next.js.
4. Add Supabase environment variables.
5. Create a Supabase client.
6. Create a form in Next.js.
7. Insert form data into the database.
8. Select and display data from the database.

---

## 2. Install Supabase

First, I installed the Supabase package in my Next.js project.

```bash
npm install @supabase/supabase-js
```

This package allows Next.js to communicate with Supabase.

---

## 3. Create `.env.local`

I created a file named:

```text
.env.local
```

I added my Supabase project URL and publishable key in this file.

```env
NEXT_PUBLIC_SUPABASE_URL=https://yourprojectid.supabase.co
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=your_publishable_key
```

These values can be found in:

**Supabase Dashboard → Project Settings → API**

The `.env.local` file keeps these values outside the main source code.

---

## 4. Create the Supabase Client

I created a file:

```text
lib/supabase.js
```

Then I imported `createClient` from Supabase.

```javascript
import { createClient } from '@supabase/supabase-js';

export const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY
);
```

This creates a connection between my Next.js application and my Supabase project.

Now I can import `supabase` into my Next.js pages and use it to insert and select database information.

---

## 5. Create the Form

I created a form in my Next.js page.

The form contains input fields where the user can enter information.

Example:

```jsx
<form action={addNote}>
  <label>Title</label>
  <input type="text" name="title" required />

  <label>Content</label>
  <textarea name="content" required />

  <button type="submit">Add Note</button>
</form>
```

The `name` attribute is important because it allows the server action to get the value entered by the user.

---

## 6. Save Information in Supabase

I created a server action called `addNote`.

```javascript
async function addNote(formData) {
  'use server';

  const title = formData.get('title');
  const content = formData.get('content');

  const { error } = await supabase
    .from('notes')
    .insert({ title, content });

  if (error) {
    throw new Error(error.message);
  }
}
```

Here:

- `formData.get('title')` gets the title from the form.
- `formData.get('content')` gets the note content.
- `.from('notes')` selects my `notes` table.
- `.insert()` adds the new note to Supabase.

---

## 7. Select Information from the Database

To get saved notes from Supabase, I used `.select()`.

```javascript
const { data: notes, error } = await supabase
  .from('notes')
  .select('*');
```

The `notes` variable contains the information received from the database.

---

## 8. Display the Notes

I used JavaScript `.map()` to display all the notes.

```jsx
{notes?.map((note) => (
  <div key={note.id}>
    <h2>{note.title}</h2>
    <p>{note.content}</p>
  </div>
))}
```

The `map()` function goes through each note and displays its title and content.

---

## 9. Refresh the Page After Adding Data

After inserting a new note, I can use `revalidatePath()` to refresh the page data.

```javascript
import { revalidatePath } from 'next/cache';

revalidatePath('/notes');
```

This makes the newly added note appear on the page.

---

## 10. Complete Process

The complete connection works like this:

```text
User
  ↓
Next.js Form
  ↓
Server Action
  ↓
Supabase Client
  ↓
Supabase Database
  ↓
Select Data
  ↓
Next.js Page
  ↓
Display Notes
```

---

## 11. Conclusion

I connected my Next.js application with Supabase using the `@supabase/supabase-js` package.

The Supabase URL and publishable key are stored in `.env.local`. The Supabase client uses these values to create the connection.

The form sends information to a server action. The server action inserts the information into the Supabase table. I then use `.select('*')` to get the saved information and display it on my Next.js page.

This allows my Note App to **save and select information from the Supabase database**.