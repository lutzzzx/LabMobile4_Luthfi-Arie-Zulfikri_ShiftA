# Tugas Pertemuan 5

- **Nama** : Luthfi Arie Zulfikri
- **NIM** : H1D022061
- **Shift Lama** : A
- **Shift Baru** : A

---

# Screenshot Halaman UI

<p align="center">
  <img src="pert4/registrasi_page.png" alt="Halaman Registrasi" width="45%" style="margin-right: 10px;" />
  <img src="pert4/login_page.png" alt="Halaman Login" width="45%" />
</p>

<p align="center">
  <img src="pert4/produk_page.png" alt="Halaman Daftar Produk" width="45%" style="margin-right: 10px;" />
  <img src="pert4/produk_detail.png" alt="Halaman Detail Produk" width="45%" />
</p>

<p align="center">
  <img src="pert4/tambah_produk.png" alt="Halaman Tambah Produk" width="45%" style="margin-right: 10px;" />
  <img src="pert4/ubah_produk.png" alt="Halaman Ubah Produk" width="45%" />
</p>

---

# Penjelasan Proses Aplikasi

## 1. Halaman Login

### a. Form Login
<p align="center">
  <img src="pert5/login.png" alt="Halaman Login" width="45%" style="margin-right: 10px;" />
</p>

Pada halaman ini, user akan memasukkan email dan password pada form login. Setelah form diisi, tombol **"Login"** ditekan, yang akan memvalidasi form terlebih dahulu. Jika validasi berhasil, fungsi `_submit()` akan dipanggil untuk mengirim data. Data ini kemudian dikirim ke fungsi `LoginBloc.login()` dan diteruskan ke server backend API untuk verifikasi.

### b. Popup Berhasil/Gagal
<p align="center">
  <img src="pert5/login_berhasil.png" alt="Halaman Login Berhasil" width="45%" style="margin-right: 10px;" />
  <img src="pert5/login_gagal.png" alt="Halaman Login Gagal" width="45%" />
</p>

Jika login berhasil (kode respon `value.code == 200`), token dan `userID` disimpan menggunakan `UserInfo()` dan user diarahkan ke halaman produk (`ProdukPage`).

```dart
if (value.code == 200) {
  await UserInfo().setToken(value.token.toString());
  await UserInfo().setUserID(int.parse(value.userID.toString()));
  Navigator.pushReplacement(
    context,
    MaterialPageRoute(builder: (context) => const ProdukPage())
  );
}
```

Jika login gagal, sebuah dialog muncul (`WarningDialog`) yang menampilkan pesan **"Login gagal, silahkan coba lagi"**.

```dart
else {
  showDialog(
    context: context,
    builder: (BuildContext context) => const WarningDialog(
      description: "Login gagal, silahkan coba lagi",
    )
  );
}
```


## 2. Halaman Tambah Produk

<p align="center">
  <img src="pert5/tambahProduk.png" alt="Halaman Tambah Produk" width="45%" style="margin-right: 10px;" />
  <img src="pert5/tambahProduk_berhasil.png" alt="Halaman Produk Berhasil" width="45%" />
</p>

Pada halaman ini, user mengisi form dengan **Kode Produk**, **Nama Produk**, dan **Harga Produk**. Setelah mengisi form, user menekan tombol **"SIMPAN"** untuk memvalidasi form.

```dart
var validate = _formKey.currentState!.validate();
if (validate) {
  if (!_isLoading) {
    simpan();
  }
}
```

Jika validasi berhasil, fungsi `simpan()` akan dipanggil untuk mengirim data. Data dikirim menggunakan `ProdukBloc.addProduk()`, yang akan diteruskan ke server backend API untuk diproses.

```dart
Produk createProduk = Produk(id: null);
createProduk.kodeProduk = _kodeProdukTextboxController.text;
createProduk.namaProduk = _namaProdukTextboxController.text;
createProduk.hargaProduk = int.parse(_hargaProdukTextboxController.text);

ProdukBloc.addProduk(produk: createProduk).then((value) {
  Navigator.of(context).push(MaterialPageRoute(
    builder: (BuildContext context) => const ProdukPage()
  ));
}, onError: (error) {
  showDialog(
    context: context,
    builder: (BuildContext context) => const WarningDialog(
      description: "Simpan gagal, silahkan coba lagi",
    )
  );
});
```

Jika simpan berhasil, pengguna akan diarahkan ke halaman produk (`ProdukPage`). Jika simpan gagal, sebuah dialog (`WarningDialog`) akan muncul dengan pesan **"Simpan gagal, silahkan coba lagi."**

## 3. Halaman Detail Produk

User dapat menekan (tap) pada salah satu produk dalam daftar untuk melihat detailnya. Saat produk dipilih, `GestureDetector` akan menangkap event tap dan menavigasi ke halaman detail produk (`ProdukDetail`) dengan mengirimkan objek produk yang dipilih.

```dart
GestureDetector(
  onTap: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => ProdukDetail(produk: produk),
      ),
    );
  },
);
```

<p align="center">
  <img src="pert5/detail_produk.png" alt="Halaman Detail Produk" width="45%" style="margin-right: 10px;" />
</p>

Di halaman detail, user dapat melihat informasi lebih lengkap tentang produk yang dipilih, seperti deskripsi, gambar, dan opsi lainnya. Ini adalah bagian dari `ProdukDetail` yang berisi detail produk yang diambil dari objek yang dikirim.


## 4. Halaman Ubah Produk

<p align="center">
  <img src="pert5/ubah_produk.png" alt="Halaman Ubah Produk" width="45%" style="margin-right: 10px;" />
</p>

Pada halaman detail produk, user melakukan tap pada tombol **"edit"** dan akan menuju form edit. User akan melihat halaman form dengan informasi produk yang sudah ada dan dapat mengubah salah satu atau lebih dari **kode produk**, **nama produk**, dan **harga**.

User menekan tombol **"UBAH"** setelah mengedit informasi produk. Ketika tombol ditekan, form akan divalidasi. Jika semua input valid, fungsi `ubah()` akan dipanggil untuk mengirim data ke server.

Fungsi `ubah()` akan membuat objek `Produk` baru dengan data yang diubah, lalu mengirim permintaan untuk memperbarui produk melalui `ProdukBloc.updateProduk`.

```dart
ProdukBloc.updateProduk(produk: updateProduk).then((value) {
  Navigator.of(context).push(MaterialPageRoute(
    builder: (BuildContext context) => const ProdukPage()
  ));
}, onError: (error) {
  showDialog(
    context: context,
    builder: (BuildContext context) => const WarningDialog(
      description: "Permintaan ubah data gagal, silahkan coba lagi",
    )
  );
});
```

<p align="center">
  <img src="pert5/ubah_produk_berhasil.png" alt="Halaman Ubah Produk Berhasil" width="45%" />
</p>

Jika berhasil, user diarahkan kembali ke halaman daftar produk (`ProdukPage`). Jika gagal, dialog peringatan ditampilkan dengan pesan kesalahan.
