# ClueScrolls

Ini adalah sebuah Clue Scrolls yang dibuat dengan Skript untuk Minecraft. Ini memungkinkan player untuk menyelesaikan serangkaian task acak yang tertulis pada sebuah scroll, kemudian memberikan reward apabila semua task berhasil diselesaikan.

---

## Cara Pakai

1. Install Skript 2.14.1+
2. Masukkan jar file ke folder plugins server
2. Masukkan file `ClueScrolls.sk` di dalam folder `plugins/Skript/scripts/`
3. Jalankan command `/skript reload ClueScrolls`
4. Berikan scroll kepada player menggunakan command `/cs give <player> <tier>`

---

## Tier Scroll

ClueScrolls ini menyediakan tiga tier scroll yang menentukan amount task dan waktu cooldown setelah scroll diselesaikan.

| Tier | Amount Clue | Cooldown |
|------|-------------|----------|
| `easy` | 3 clue | 5 menit |
| `medium` | 5 clue | 10 menit |
| `hard` | 7 clue | 20 menit |

Material item scroll berbeda untuk setiap tier. Tier easy menggunakan `paper`, medium menggunakan `book`, dan hard menggunakan `enchanted book`.

---

## Command

| Command | Description | Permission |
|----------|------------|------------|
| `/cs` | Menampilkan list command yang tersedia |  |
| `/cs scroll` | Menampilkan progress scroll yang sedang aktif |  |
| `/cs stats [player]` | Menampilkan statistik player | `cluescrolls.admin` untuk melihat statistik player lain |
| `/cs abandon` | Meninggalkan scroll aktif (progress akan terhapus) |  |
| `/cs leaderboard` | Menampilkan 10 player teratas |  |
| `/cs give <player> <tier>` | Memberikan scroll kepada player | `cluescrolls.give` atau OP |
| `/cs reload` | Mereset pool clue dan reward | `cluescrolls.reload` atau OP |
| `/cs forcecomplete <player>` | Memaksa menyelesaikan scroll milik player | OP only |
| `/cs resetcooldown <player> [tier]` | Mereset cooldown scroll player | OP only |

Command ini dapat diakses melalui alias `/cluescrolls`, `/cs`, maupun `/clue`.

---

## Cara Penggunaan dari Sisi Player

1. Dapatkan scroll dari admin atau dari event server
2. Klik kanan pada scroll untuk memulai, clue pertama akan muncul di lore item
3. Selesaikan task yang diberikan, seperti membunuh mob atau menghancurkan block
4. Setelah task selesai, klik kanan kembali pada scroll untuk melanjutkan ke clue berikutnya
5. Selesaikan semua clue dan reward akan diberikan secara otomatis

Scroll dapat dijeda dengan cara memindahkannya keluar dari inventory atau dengan keluar dari server. Progress tetap tersimpan dan player dapat melanjutkan kembali dengan mengeklik kanan scroll tersebut.

---

## Type Task

Berikut adalah list type task yang tersedia beserta descriptionnya.

| Type | Description |
|------|-----------|
| `kill` | Membunuh mob tertentu |
| `break` | Menghancurkan block tertentu |
| `place` | Memasang block tertentu |
| `craft` | Membuat item tertentu |
| `eat` | Memakan makanan tertentu |
| `fish` | Memancing ikan |
| `swim` | Berenang sejumlah blok |
| `dive` | Menyelam sejumlah detik |
| `enchant` | Melakukan enchant pada item |
| `smelt` | Melebur item di dalam furnace |
| `brew` | Membuat potion |
| `trade` | Berdagang dengan Villager |
| `tame` | Menjinakkan animal |
| `collect` | Mengambil item dari tanah |
| `open` | Membuka chest |
| `shoot` | Menembak mob menggunakan panah |
| `explore` | Menjelajah sejumlah blok dari spawn |
| `chat` | Mengirim pesan di chat |
| `sleep` | Tidur di kasur |
| `jump` | Melompat sejumlah kali |
| `die` | Mati (ini adalah task yang sesungguhnya) |

Untuk parameter `arg`, isi dengan nama mob, block, atau item yang spesifik. Gunakan `"any"` apabila semua type ingin dihitung.

---

## Menambahkan Clue Baru

Buka bagian `cs_init_clues()` di dalam file `.sk`, kemudian tambahkan line berikut:

```
cs_register("type", "target", amount, "tier")
```

Contoh:
```
cs_register("kill", "phantom", 3, "medium")
cs_register("collect", "any", 64, "easy")
cs_register("break", "obsidian", 10, "hard")
```

ID clue akan dibuat secara otomatis dan deskripsi task juga akan digenerate berdasarkan type yang diberikan, sehingga tidak perlu menulisnya secara manual.

---

## Menambahkan Reward Baru

Buka bagian `cs_init_rewards()`, kemudian tambahkan kode berikut:

```
cs_add_reward("tier", "nama_id", "command", "command console", bobot)
```

- **bobot** menentukan seberapa sering reward tersebut akan muncul, semakin besar nilainya, semakin sering reward itu diberikan
- `{player}` di dalam string command akan diganti secara otomatis dengan nama player yang bersangkutan

Contoh:
```
cs_add_reward("easy", "bread_1", "command", "give {player} bread 10", 80)
cs_add_reward("hard", "totem_2", "command", "give {player} totem_of_undying 2", 5)
```

---

## Variable Skript

Berikut adalah variabel yang digunakan.

```
{cs::active::%uuid%}
  → UID scroll yang sedang aktif milik player
    Kosong atau tidak diset berarti player tidak memiliki scroll aktif

{cs::session::%uid%::owner}            → UUID pemilik scroll
{cs::session::%uid%::tier}             → Tier scroll (easy / medium / hard)
{cs::session::%uid%::clues::*}         → List clue ID yang dipilih saat scroll dibuka
{cs::session::%uid%::current_clue}     → Clue yang sedang aktif dan dikerjakan
{cs::session::%uid%::progress::%cid%}  → Nilai progress untuk setiap clue
{cs::session::%uid%::completed}        → Amount clue yang sudah diselesaikan
{cs::session::%uid%::total}            → Total clue yang harus diselesaikan

{cs::stats::%uuid%::scrolls_completed} → Total scroll yang telah diselesaikan
{cs::stats::%uuid%::clues_completed}   → Total clue yang telah diselesaikan
{cs::stats::%uuid%::scrolls_easy}      → Amount scroll easy yang diselesaikan
{cs::stats::%uuid%::scrolls_medium}    → Amount scroll medium yang diselesaikan
{cs::stats::%uuid%::scrolls_hard}      → Amount scroll hard yang diselesaikan

{cs::cooldown::%uuid%::%tier%}         → Timestamp terakhir scroll diselesaikan (untuk cooldown)
{cs::lastquit::%uuid%}                 → Timestamp terakhir player keluar dari server
```