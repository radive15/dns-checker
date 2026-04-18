# dnslook

Alat pencarian DNS berbasis command-line yang ditulis dalam Go. Melakukan query record A, AAAA, MX, TXT, CNAME, dan NS dari domain mana pun — dengan output yang bersih dan mudah dibaca.

```
$ dnslook -domain github.com -type A
Querying A records for github.com...

A Records
─────────────────────────
  140.82.112.4   TTL: 60s
  140.82.114.4   TTL: 60s

Query completed in 38ms
```

---

## Fitur

- Mendukung tipe record: `A`, `AAAA`, `MX`, `TXT`, `CNAME`, `NS`
- Dukungan DNS resolver kustom (misalnya gunakan `8.8.8.8` atau `1.1.1.1`)
- Tampilan waktu query
- Output terminal yang bersih dan mudah dibaca
- Tanpa dependensi eksternal (hanya menggunakan paket `net` dari Go)

---

## Instalasi

**Persyaratan:** Go 1.21+

```bash
git clone https://github.com/yourusername/dns-checker.git
cd dns-checker
go build -o dns-checker .
```

---

## Penggunaan

```bash
# Query record A (default)
./dns-checker -domain example.com

# Query tipe record tertentu
./dns-checker -domain example.com -type MX
./dns-checker -domain example.com -type TXT
./dns-checker -domain example.com -type AAAA

# Gunakan resolver kustom
./dns-checker -domain example.com -resolver 8.8.8.8

# Query semua tipe record sekaligus
./dns-checker -domain example.com -all
```

### Semua flag

| Flag | Default | Deskripsi |
|------|---------|-----------|
| `-domain` | *(wajib)* | Nama domain yang akan di-query |
| `-type` | `A` | Tipe record: A, AAAA, MX, TXT, CNAME, NS |
| `-resolver` | Default sistem | IP DNS resolver (misalnya `8.8.8.8`) |
| `-all` | `false` | Query semua tipe record yang didukung |
| `-timeout` | `5s` | Durasi timeout query |

---

## Contoh Output

```
$ ./dns-checker -domain gmail.com -type MX

Querying MX records for gmail.com...

MX Records
─────────────────────────────────────
  Priority: 5    gmail-smtp-in.l.google.com
  Priority: 10   alt1.gmail-smtp-in.l.google.com
  Priority: 20   alt2.gmail-smtp-in.l.google.com
  Priority: 30   alt3.gmail-smtp-in.l.google.com
  Priority: 40   alt4.gmail-smtp-in.l.google.com

Query completed in 52ms
```

---

## Struktur Proyek

```
dns-checker/
├── main.go          # Titik masuk, parsing flag
├── resolver.go      # Logika query DNS per tipe record
├── formatter.go     # Pemformatan output
├── resolver_test.go
├── Makefile
└── README.md
```

---

## Menjalankan Tes

```bash
make test
# atau
go test ./...
```

---

## Makefile

```bash
make build   # Build binary
make test    # Jalankan tes
make clean   # Hapus binary
```

---

## Cara Kerja

Paket `net` dari Go menyediakan `LookupHost`, `LookupMX`, `LookupTXT`, dan lainnya. Untuk dukungan resolver kustom, alat ini membuat `net.Resolver` kustom dengan fungsi dial yang ditentukan — mengarah ke IP DNS yang dipilih pada port 53.

```go
r := &net.Resolver{
    PreferGo: true,
    Dial: func(ctx context.Context, network, address string) (net.Conn, error) {
        d := net.Dialer{Timeout: timeout}
        return d.DialContext(ctx, "udp", resolverAddr+":53")
    },
}
```

---

## Tujuan Pembelajaran

Proyek ini dibuat untuk berlatih:

- Menggunakan paket `net` dari Go untuk query DNS
- Parsing flag CLI dan validasi input
- Memformat dan menampilkan output terstruktur
- Memahami cara kerja resolusi DNS di tingkat kode

---

## Lisensi

MIT
