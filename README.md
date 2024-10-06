
### Install Laravel
1. klik kanan git bash `composer create-project laravel/laravel (nama folder)`
2. masuk ke direktori laravel `cd (nama folder)`
3. set .env
    ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=crud_mahasiswa
    DB_USERNAME=root
    DB_PASSWORD=
    ```
4. buat db crud_mahasiswa di mysql
5. jalankan laravel `php artisan serve`
### Install Brezze
1. `composer require laravel/breeze --dev`
2. `php artisan breeze:install` pilih `blade`, untuk dark mode `no`, pilih `1`
3. `php artisan migrate`
4. `npm install`
5. `npm run dev`

buka halaman laravel dan masuk ke halaman register untuk menambahkan user, setelah itu login

### Push ke Repository Github
1. BUAT AKUN GITHUB
2. buat repository di GITHUB
3. pada direktori laravel yang telah dibuat push ke repository
    ```
    git init
    git remote add origin (link repository nya)
    git add .
    git commit -m "catatan"
    git push -u origin main
    ```

4. mengupdate perubahan pada repository
    ```
    git add .
    git commit -m "catatan"
    git push
    ```

5. untuk mengembalikan riwayat commit 
    ```
    git log
    git revert <commit id>
    git push origin master
    ```

### Setup Halaman Program Studi
1. buat file migration untuk tabel prodi `php artisan make:migration create_prodis_table`
2. pada file migration untuk tabel prodi masukan query berikut pada public function up
    ```
    Schema::create('prodis', function (Blueprint $table) {
        $table->id();
        $table->string('nama');
        $table->timestamps();
    });
    ```
3. migrasikan tabel prodi `php artisan migration`
4. buat file model untuk tabel prodi `php artisan make:model Prodi`
pada file model prodi class prodi masukan query berikut 
    ```
    protected $fillable = ['nama'];
    ```
5. buat file controller untuk prodi `php artisan make:controller`
pada file ProdiController masukan query koneksi ke model prodi dan function index
    ```
    use App\Models\Prodi;
    ................
    public function index()
    {
        return view('prodi.index');
    }
    ```
6. buat folder prodi dan buat file untuk view index halaman prodi `index.blade.php` masukan query berikut
    ```
    <x-app-layout>
        <x-slot name="header">
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                {{ __('Program Studi') }}
            </h2>
        </x-slot>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <div class="d-flex justify-content-between align-items-center mb-3">
                            <div class="ml-auto d-flex">
                                <a href="#" class="btn btn-primary mr-2">Tambah Program Studi</a>
                                <form action="" method="GET" class="d-flex">
                                    <input type="text" name="search" class="form-control" placeholder="Pencarian">
                                    <button class="btn btn-primary ml-2" type="submit">
                                        <i class="bi bi-search"></i>
                                    </button>
                                </form>
                            </div>
                        </div>

                        <table class="table table-hover">
                            <thead class="table-primary">
                                <tr>
                                    <th>No</th>
                                    <th>Nama Program Studi</th>
                                    <th>Aksi</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td></td>
                                    <td></td>
                                    <td>
                                        <a href="#"class="btn btn-secondary">Edit</a>
                                        <a href="#"class="btn btn-danger">Hapus</a>
                                    </td>
                                </tr>
                            </tbody>
                        </table>

                    </div>
                </div>
            </div>
        </div>
    </x-app-layout>
    ```
7. pada file `views/layouts/app.blade.php` tambahkan script berikut untuk library js dan bootstrap
    ```
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.1/jquery.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons/font/bootstrap-icons.css" rel="stylesheet">
    ```
8. pada `web.php` tambahkan route untuk halaman prodi index
    ```
    Route::get('/prodi', [ProdiController::class, 'index'])->name('/prodi')
    ```

### Setup Halaman Program Studi - Fungsi Tambah Data (add)
1. pada file `ProdiController.php` di folder app/http/controller tambahkan function class create dan save, serta pada function class index
    ```
    public function index()
    {
        $prodis = Prodi::orderBy('id', 'desc')->get();
        return view('prodi.index', compact('prodis'));
    }

    public function create()
    {
        return view('prodi.create');
    }

    public function save(Request $request)
    {
        $request->validate([
            'nama' => 'required'
        ]);

        Prodi::create([
            'nama' => $request->nama
        ]);

        return redirect()->route('prodi')->with('success', 'Program Studi berhasil ditambahkan');
    }
    ```
2. pada file `web.php` di folder routes tambahkan 2 route untuk create dan save
    ```
    Route::get('/prodi/create', [ProdiController::class, 'create'])->name('prodi/create');
    Route::post('/prodi/save', [ProdiController::class, 'save'])->name('prodi/save');
    ```
3. buat file `create.blade.php` di folder view/prodi untuk view halaman form tambah data masukan query berikut
    ```
    <x-app-layout>
    <x-slot name="header">
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">
            {{ __('Tambah Data Program Studi') }}
        </h2>
    </x-slot>

    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                <div class="p-6 text-gray-900">
                    <form action="{{ route('prodi/save') }}" method="POST">
                        @csrf
                        <div class="mb-3">
                            <label for="nama" class="form-label">Nama Program Studi</label>
                            <input type="text" class="form-control" id="nama" name="nama">
                            @error('nama')
                                <div class="text-danger">{{ $message }}</div>
                            @enderror
                        </div>
                        <button type="submit" class="btn btn-primary">Simpan</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
    </x-app-layout>

    ```
4. update file `index.blade.php` agar dapat menampilkan data dari database dan beberapa fungsi lainnya
    ```
    <x-app-layout>
    <x-slot name="header">
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">
            {{ __('Program Studi') }}
        </h2>
    </x-slot>

    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                <div class="p-6 text-gray-900">
                    <div class="d-flex justify-content-between align-items-center mb-3">
                        @if (Session::has('success'))
                            <div class="alert alert-success">
                                {{ Session::get('success') }}
                            </div>
                        @endif
                        <div class="ml-auto d-flex">
                            <a href="{{ route('prodi/create') }}" class="btn btn-primary mr-2">Tambah Program Studi</a>
                            <form action="" method="GET" class="d-flex">
                                <input type="text" name="search" class="form-control" placeholder="Pencarian">
                                <button class="btn btn-primary ml-2" type="submit">
                                    <i class="bi bi-search"></i>
                                </button>
                            </form>
                        </div>
                    </div>

                    <table class="table table-hover">
                        <thead class="table-primary">
                            <tr>
                                <th>No</th>
                                <th>Nama Program Studi</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            @foreach ($prodis as $prodi)
                                <tr>
                                    <td>{{ $loop->iteration }}</td>
                                    <td>{{ $prodi->nama }}</td>
                                    <td>
                                        <a href="#"class="btn btn-secondary">Edit</a>
                                        <a href="#"class="btn btn-danger">Hapus</a>
                                    </td>
                                </tr>
                            @endforeach
                        </tbody>
                    </table>

                </div>
            </div>
        </div>
    </div>
    </x-app-layout>
    ```


