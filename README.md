# Bab 6 - Kotlin Coroutines dengan Firebase Authentication

## Tujuan
Mahasiswa dapat memahami konsep dasar Kotlin Coroutines dan menerapkannya dalam aplikasi Android untuk menjalankan operasi asinkron, seperti mengambil data dari Firebase atau REST API, tanpa memblokir thread utama dan menjaga UI tetap responsif.

## Fitur yang Diimplementasikan

### 1. **Kotlin Coroutines**
   - Menangani operasi asinkron dengan coroutines
   - Menggunakan `withContext(Dispatchers.IO)` untuk operasi Firebase
   - Implementasi `suspend` functions dengan `await()`

### 2. **Firebase Authentication**
   - Login dengan email dan password
   - Persistensi state autentikasi pengguna
   - Logout functionality

### 3. **Navigation Compose**
   - Navigasi antar screen
   - Conditional navigation berdasarkan authentication state
   - Clear back stack saat login/logout

## Struktur Proyek

```
app/src/main/java/com/example/coroutines/
├── MainActivity.kt                              # Entry point aplikasi
├── navigation/
│   ├── Screen.kt                                # Sealed class untuk routing
│   └── AppNavigation.kt                         # Navigation graph
└── ui/
    ├── screens/
    │   ├── LoginScreen.kt                       # Screen login dengan coroutines
    │   └── RekomendasiTempatScreen.kt          # Screen rekomendasi tempat
    └── theme/
        └── ...                                  # Theme files
```

## Cara Kerja Aplikasi

### A. Authentication State Checking
Saat aplikasi dibuka, `MainActivity` akan mengecek apakah ada pengguna yang sedang login:
```kotlin
val currentUser: FirebaseUser? = FirebaseAuth.getInstance().currentUser
```

### B. Conditional Navigation
Berdasarkan `currentUser`, aplikasi akan mengarahkan ke:
- **Login Screen** - jika `currentUser == null`
- **Rekomendasi Tempat Screen** - jika pengguna sudah login

### C. Login dengan Coroutines
Login menggunakan Kotlin Coroutines untuk menangani operasi asinkron:
```kotlin
coroutineScope.launch {
    try {
        withContext(Dispatchers.IO) {
            FirebaseAuth.getInstance()
                .signInWithEmailAndPassword(email, password)
                .await()
        }
        onLoginSuccess()
    } catch (e: Exception) {
        errorMessage = "Login failed: ${e.localizedMessage}"
    }
}
```

### D. Persistensi State
Firebase Authentication secara otomatis menyimpan state login pengguna. Ketika aplikasi ditutup dan dibuka kembali, pengguna tidak perlu login lagi.

## Dependencies yang Digunakan

```kotlin
// Kotlin Coroutines
implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.6.4")

// Firebase
implementation(platform("com.google.firebase:firebase-bom:33.7.0"))
implementation("com.google.firebase:firebase-auth")

// Navigation Compose
implementation("androidx.navigation:navigation-compose:2.7.5")
```

## Setup Firebase

### Langkah-langkah:
1. Buka [Firebase Console](https://console.firebase.google.com/)
2. Buat project baru atau gunakan existing project
3. Tambahkan Android app dengan package name: `com.example.coroutines`
4. Download file `google-services.json`
5. Replace file `google-services.json` di folder `app/` dengan file yang sudah didownload
6. Aktifkan **Email/Password Authentication** di Firebase Console:
   - Pergi ke Authentication > Sign-in method
   - Enable Email/Password provider
7. Tambahkan test user di Authentication > Users

## Cara Menjalankan Aplikasi

1. **Sync Gradle**
   - Klik "Sync Now" atau jalankan: `./gradlew sync`

2. **Build Project**
   - Build > Make Project atau `./gradlew build`

3. **Run Aplikasi**
   - Pilih device/emulator
   - Klik Run atau tekan Shift + F10

## Testing

### Test Login:
1. Buka aplikasi
2. Masukkan email dan password yang sudah didaftarkan di Firebase
3. Klik tombol Login
4. Jika berhasil, akan diarahkan ke screen Rekomendasi Tempat

### Test Persistensi:
1. Login ke aplikasi
2. Tutup aplikasi (kill dari recent apps)
3. Buka kembali aplikasi
4. Aplikasi akan langsung menampilkan Rekomendasi Tempat Screen (tidak perlu login lagi)

### Test Logout:
1. Di Rekomendasi Tempat Screen, klik tombol Logout
2. Akan kembali ke Login Screen
3. Firebase authentication state di-clear

## Keuntungan Kotlin Coroutines

1. **Kode Lebih Bersih**: Tidak perlu callback yang nested
2. **Sequential Code**: Menulis kode asinkron seperti kode synchronous
3. **Exception Handling**: Menggunakan try-catch seperti biasa
4. **Thread Management**: Mudah switch context dengan `Dispatchers`
5. **Non-Blocking**: UI tetap responsif saat operasi background berjalan

## Troubleshooting

### Error: google-services.json not found
- Pastikan file `google-services.json` ada di folder `app/`
- File harus valid dari Firebase Console

### Error: FirebaseException
- Pastikan Firebase Authentication sudah diaktifkan
- Periksa email/password yang diinput sudah terdaftar

### Error: Coroutines not working
- Pastikan dependency coroutines sudah ditambahkan
- Check import statements untuk `launch`, `withContext`, `await()`

## Catatan Penting

⚠️ **PENTING**: File `google-services.json` yang disertakan adalah contoh template. Anda HARUS mengganti dengan file asli dari Firebase Console Anda sendiri agar aplikasi berfungsi dengan benar.

## Referensi

- [Kotlin Coroutines Official Documentation](https://kotlinlang.org/docs/coroutines-overview.html)
- [Firebase Authentication](https://firebase.google.com/docs/auth/android/start)
- [Jetpack Compose Navigation](https://developer.android.com/jetpack/compose/navigation)

