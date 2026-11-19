# Dokumentasi Perubahan Proyek

Dokumen ini mencatat perubahan yang dilakukan untuk menyelesaikan tugas (TODO) pada proyek CODELAB-4-WEB.

## 1. Migrasi Database
**File:** `database/migrations/2026_08_10_043505_create_members_table.php`

**Perubahan:**
- Menambahkan kolom `name` bertipe `string` ke dalam tabel `members`.

**Kode:**
```php
public function up(): void
{
    Schema::create('members', function (Blueprint $table) {
        $table->id();
        $table->string('name'); // ditambah
        $table->timestamps();
    });
}
```

## 2. Model Member
**File:** `app/Models/Member.php`

**Perubahan:**
- Menambahkan properti `$fillable` dan definisi relasi `hasMany`.

**Kode:**
```php
class Member extends Model
{
    protected $fillable = ['name']; // melengkapi

    public function books()
    {
        return $this->hasMany(Book::class); // melengkapi
    }
}
```

## 3. Controller Member
**File:** `app/Http/Controllers/MemberController.php`

**Perubahan:**
- Implementasi logika peminjaman dan pengembalian buku.

**Kode:**
```php
    public function borrowBook(Request $request, Member $member)
    {
        $request->validate([
            'book_id' => 'required|exists:books,id',
        ]);

        $book = Book::find($request->book_id); // ditambah

        if (!$book->is_available) { // melengkapi
            return response()->json(['message' => 'Buku tidak tersedia'], 400); // ditambah
        }

        $book->update([ // melengkapi
            'member_id' => $member->id,
            'is_available' => false
        ]);

        return response()->json(['message' => 'Buku berhasil dipinjam']); // melengkapi
    }

    public function returnBook(Member $member, Book $book)
    {
        if ($book->member_id !== $member->id) { // melengkapi
            return response()->json(['message' => 'Buku tidak sesuai peminjam'], 403); // ditambah
        }

        $book->update([ // melengkapi
            'member_id' => null,
            'is_available' => true
        ]);

        return response()->json(['message' => 'Buku berhasil dikembalikan']); // melengkapi
    }
```

## 4. Resource Member
**File:** `app/Http/Resources/MemberResource.php`

**Perubahan:**
- Menambahkan field `name` dan relasi `books`.

**Kode:**
```php
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name, // ditambah
            'books' => BookResource::collection($this->whenLoaded('books')), // ditambah
        ];
    }
```

---
*Dokumentasi ini dibuat secara otomatis setelah penyelesaian tugas.*
