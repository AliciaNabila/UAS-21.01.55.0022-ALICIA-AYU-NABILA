# UAS-21.01.55.0022-ALICIA-AYU-NABILA

### login.php ###
```
<?php
session_start();
require_once 'config.php';

if(isset($_POST['login'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];
    
    // Gunakan fungsi PASSWORD() untuk mencocokkan dengan hash di database
    $query = "SELECT * FROM users WHERE username='$username' AND password=PASSWORD('$password')";
    $result = mysqli_query($conn, $query);
    
    if(mysqli_num_rows($result) == 1) {
        $row = mysqli_fetch_assoc($result);
        $_SESSION['user_id'] = $row['id'];
        $_SESSION['username'] = $row['username'];
        $_SESSION['name'] = $row['name'];
            
        header("Location: index.php");
        exit();
    } else {
        $error = "Username atau Password salah!";
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>
<body>
    <div style="width: 100%; max-width: 400px; margin: 100px auto; padding: 20px; border: 1px solid #ccc; border-radius: 10px; background-color: #f9f9f9;">
        <h2 style="text-align: center; color: #007bff;">Login Form</h2>

        <?php if(isset($error)) { ?>
            <p style="color: red; text-align: center;"><?php echo $error; ?></p>
        <?php } ?>
        
        <form action="" method="POST" style="text-align: center;">
            <div style="margin-bottom: 15px;">
                <label for="username" style="font-weight: bold; color: #333;">Username:</label>
                <input type="text" id="username" name="username" required style="padding: 10px; width: 80%; border: 1px solid #ccc; border-radius: 5px;">
            </div>

            <div style="margin-bottom: 20px;">
                <label for="password" style="font-weight: bold; color: #333;">Password:</label>
                <input type="password" id="password" name="password" required style="padding: 10px; width: 80%; border: 1px solid #ccc; border-radius: 5px;">
            </div>

            <div>
                <input type="submit" name="login" value="Login" style="padding: 10px 20px; background-color: #007bff; color: white; border: none; border-radius: 5px; cursor: pointer;">
            </div>
        </form>
    </div>
</body>
</html>
```

### config.php ###
```
<?php
$host = "localhost";
$dbuser = "root";
$dbpass = "";
$dbname = "ticketstore";

$conn = mysqli_connect($host, $dbuser, $dbpass, $dbname);
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
?>
```

### index.php ###
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daftar Tiket</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #e3f2fd;  /* Light blue background color */
            margin-top: 70px;
        }
    
        .navbar {
            background-color: #003366; /* Dark blue navbar */
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            width: 100%;
            z-index: 1000;
            padding: 15px 0;
        }
    
        .navbar-brand {
            font-size: 2rem;
            font-weight: bold;
            color: white !important;
        }
    
        .navbar-brand:hover {
            color: #ffcc00 !important;
        }
    
        .navbar-nav .nav-link {
            color: white !important;
            font-size: 1.2rem;
            padding: 12px 25px;
            transition: background-color 0.3s ease;
        }
    
        .navbar-nav .nav-link:hover {
            color: #ffcc00 !important;
            background-color: #00509e;
            border-radius: 5px;
        }
    
        .navbar-nav .nav-link.logout-btn {
            background-color: #dc3545;
            color: white;
            padding: 10px 25px;
            border-radius: 25px;
            font-size: 1.2rem;
        }
    
        .navbar-nav .nav-link.logout-btn:hover {
            background-color: #c82333;
        }
    
        .container {
            width: 100%;
            padding: 20px;
        }
    
        h1 {
            font-size: 3rem;
            color: #003366;  /* Dark blue color for heading */
            text-align: center;
            margin-bottom: 40px;
        }
    
        .card {
            border-radius: 15px;
            box-shadow: 0 2px 15px rgba(0, 0, 0, 0.1);
            margin-bottom: 40px;
        }
    
        .table {
            background-color: white;
            border-radius: 10px;
            overflow: hidden;
            width: 100%;
        }
    
        .table th, .table td {
            vertical-align: middle;
            padding: 15px 20px;
        }
    
        .table th {
            background-color: #003366;
            color: white;
        }
    
        .table td {
            background-color: #f8f9fa;
        }
    
        .btn-primary, .btn-success, .btn-warning, .btn-danger {
            border-radius: 25px;
            padding: 12px 30px;
        }
    
        .btn-primary {
            background-color: #003366;
        }
    
        .btn-primary:hover {
            background-color: #00509e;
        }
    
        .btn-success {
            background-color: #28a745;
        }
    
        .btn-success:hover {
            background-color: #218838;
        }
    
        .btn-warning {
            background-color: #ffc107;
        }
    
        .btn-warning:hover {
            background-color: #e0a800;
        }
    
        .btn-danger {
            background-color: #dc3545;
        }
    
        .btn-danger:hover {
            background-color: #c82333;
        }
    
        .search-bar {
            display: flex;
            justify-content: space-between;
            margin-bottom: 25px;
        }
    
        .search-bar .form-control {
            max-width: 350px;
        }
    </style>
    </head>

<body class="container py-4">
    <nav class="navbar navbar-expand-lg navbar-light">
        <a class="navbar-brand" href="#">Daftar Tiket Pesawat</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" href="home.php">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="index.php">Daftar Tiket Pesawat</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="contact.php">Kontak</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link logout-btn" href="javascript:logout()">Logout</a>
                </li>
            </ul>
        </div>
    </nav>

    <h1>Selamat Datang di Daftar Tiket Pesawat</h1>

<div class="card p-4 mb-4">
    <h5>Kenapa menggunakan Daftar Tiket Pesawat?</h5>
    <p>Website ini menyediakan daftar lengkap tiket pesawat yang dapat diakses oleh masyarakat. Anda dapat mencari tiket pesawat berdasarkan ID, tujuan, tanggal keberangkatan, harga, atau stok yang tersedia. Tambahkan tiket pesawat baru, edit informasi tiket yang sudah ada, atau hapus data tiket yang tidak diperlukan untuk memastikan data yang akurat dan terkini.</p>
</div>

  
    <div class="row mb-3">
        <div class="col">
            <input type="text" id="searchInput" class="form-control" placeholder="Cari berdasarkan ID">
        </div>
        <div class="col-auto">
            <button onclick="searchTicket()" class="btn btn-primary">Cari</button>
        </div>
        <div class="col-auto">
            <button type="button" class="btn btn-success" data-bs-toggle="modal" data-bs-target="#ticketModal">
                Tambah Tiket
            </button>
        </div>
    </div>

    <table class="table table-striped">
        <thead class="table-dark">
            <tr>
                <th>ID</th>
                <th>Tujuan</th>
                <th>Tanggal</th>
                <th>Harga</th>
                <th>Stok</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody id="ticketList">
        </tbody>
    </table>

    <!-- Modal untuk Tambah/Edit Tiket -->
    <div class="modal fade" id="ticketModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalTitle">Tambah Tiket</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="ticketForm">
                        <input type="hidden" id="ticketId">
                        <div class="mb-3">
                            <label for="destination" class="form-label">Tujuan</label>
                            <input type="text" class="form-control" id="destination" required>
                        </div>
                        <div class="mb-3">
                            <label for="date" class="form-label">Tanggal</label>
                            <input type="text" class="form-control" id="date" required>
                        </div>
                        <div class="mb-3">
                            <label for="price" class="form-label">Harga</label>
                            <input type="number" class="form-control" id="price" required>
                        </div>
                        <div class="mb-3">
                            <label for="stock" class="form-label">Stok</label>
                            <input type="number" class="form-control" id="stock" required>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Batal</button>
                    <button type="button" class="btn btn-primary" onclick="saveTicket()">Simpan</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        const API_URL = 'http://localhost/rest_tickets/tickets_api.php';
        let ticketModal;

        document.addEventListener('DOMContentLoaded', function() {
            ticketModal = new bootstrap.Modal(document.getElementById('ticketModal'));
            loadTickets(); // Memuat semua tiket saat halaman pertama kali dibuka
        });

        function loadTickets() {
            fetch(API_URL)
                .then(response => response.json())
                .then(tickets => {
                    const ticketList = document.getElementById('ticketList');
                    ticketList.innerHTML = '';
                    tickets.forEach(ticket => {
                        ticketList.innerHTML += 
                            `<tr>
                                <td>${ticket.id}</td>
                                <td>${ticket.destination}</td>
                                <td>${ticket.date}</td>
                                <td>${ticket.price}</td>
                                <td>${ticket.stock}</td>
                                <td class="btn-group-action">
                                    <button class="btn btn-sm btn-warning me-1" onclick="editTicket(${ticket.id})">Edit</button>
                                    <button class="btn btn-sm btn-danger" onclick="deleteTicket(${ticket.id})">Hapus</button>
                                </td>
                            </tr>`;
                    });
                })
                .catch(error => alert('Error loading tickets: ' + error));
        }

        function searchTicket() {
            const id = document.getElementById('searchInput').value.trim();
            if (!id) {
                loadTickets(); // Jika input ID kosong, tampilkan semua tiket
                return;
            }

            // URL untuk mencari tiket berdasarkan ID
            const url = `${API_URL}/${id}`;

            fetch(url)
                .then(response => response.json())
                .then(ticket => {
                    const ticketList = document.getElementById('ticketList');
                    ticketList.innerHTML = '';

                    if (ticket.message) {
                        alert('Ticket not found');
                        return;
                    }

                    ticketList.innerHTML = 
                        `<tr>
                            <td>${ticket.id}</td>
                            <td>${ticket.destination}</td>
                            <td>${ticket.date}</td>
                            <td>${ticket.price}</td>
                            <td>${ticket.stock}</td>
                            <td class="btn-group-action">
                                <button class="btn btn-sm btn-warning me-1" onclick="editTicket(${ticket.id})">Edit</button>
                                <button class="btn btn-sm btn-danger" onclick="deleteTicket(${ticket.id})">Hapus</button>
                            </td>
                        </tr>`;
                })
                .catch(error => alert('Error searching ticket: ' + error));
        }

        function editTicket(id) {
            fetch(`${API_URL}/${id}`)
                .then(response => response.json())
                .then(ticket => {
                    document.getElementById('ticketId').value = ticket.id;
                    document.getElementById('destination').value = ticket.destination;
                    document.getElementById('date').value = ticket.date;
                    document.getElementById('price').value = ticket.price;
                    document.getElementById('stock').value = ticket.stock;
                    document.getElementById('modalTitle').textContent = 'Edit Tiket';
                    ticketModal.show();
                })
                .catch(error => alert('Error loading ticket details: ' + error));
        }

        function deleteTicket(id) {
            if (confirm('Are you sure you want to delete this ticket?')) {
                fetch(`${API_URL}/${id}`, { method: 'DELETE' })
                    .then(response => response.json())
                    .then(data => {
                        alert('Ticket deleted successfully');
                        loadTickets();
                    })
                    .catch(error => alert('Error deleting ticket: ' + error));
            }
        }

        function saveTicket() {
            const ticketId = document.getElementById('ticketId').value;
            const ticketData = {
                destination: document.getElementById('destination').value,
                date: document.getElementById('date').value,
                price: document.getElementById('price').value,
                stock: document.getElementById('stock').value
            };

            const method = ticketId ? 'PUT' : 'POST';
            const url = ticketId ? `${API_URL}/${ticketId}` : API_URL;

            fetch(url, {
                method: method,
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(ticketData)
            })
            .then(response => response.json())
            .then(data => {
                alert(ticketId ? 'Ticket updated successfully' : 'Ticket added successfully');
                ticketModal.hide();
                loadTickets();
                resetForm();
            })
            .catch(error => alert('Error saving ticket: ' + error));
        }

        function resetForm() {
            document.getElementById('ticketId').value = '';
            document.getElementById('ticketForm').reset();
            document.getElementById('modalTitle').textContent = 'Tambah Tiket';
        }

        // Reset form when modal is closed
        document.getElementById('ticketModal').addEventListener('hidden.bs.modal', resetForm);
    </script>
</body>
</html>
```

### home.php ###
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home - Tiket Pesawat</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600&display=swap" rel="stylesheet">
<style>
    body {
        font-family: 'Nunito', sans-serif;
        background-color: #e3f2fd;  /* Light blue background color */
        margin-top: 70px;
    }

    .navbar {
        background-color: #003366; /* Dark blue navbar */
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        width: 100%;
        z-index: 1000;
        padding: 15px 0;
    }

    .navbar-brand {
        font-size: 2rem;
        font-weight: bold;
        color: white !important;
    }

    .navbar-brand:hover {
        color: #ffcc00 !important;
    }

    .navbar-nav .nav-link {
        color: white !important;
        font-size: 1.2rem;
        padding: 12px 25px;
        transition: background-color 0.3s ease;
    }

    .navbar-nav .nav-link:hover {
        color: #ffcc00 !important;
        background-color: #00509e;
        border-radius: 5px;
    }

    .navbar-nav .nav-link.logout-btn {
        background-color: #dc3545;
        color: white;
        padding: 10px 25px;
        border-radius: 25px;
        font-size: 1.2rem;
    }

    .navbar-nav .nav-link.logout-btn:hover {
        background-color: #c82333;
    }

    .container {
        width: 100%;
        padding: 20px;
    }

    h1 {
        font-size: 3rem;
        color: #003366;  /* Dark blue color for heading */
        text-align: center;
        margin-bottom: 40px;
    }

    .home-content {
        text-align: center;
        padding: 40px 20px;
    }

    .home-content h2 {
        font-size: 2.5rem;
        color: #003366;
        margin-bottom: 30px;
    }

    .home-content p {
        font-size: 1.2rem;
        color: #333;
    }

    .btn-primary {
        background-color: #003366;
        padding: 12px 30px;
        border-radius: 25px;
    }

    .btn-primary:hover {
        background-color: #00509e;
    }

    .home-card {
        background-color: white;
        border-radius: 15px;
        box-shadow: 0 2px 15px rgba(0, 0, 0, 0.1);
        padding: 30px;
        margin-bottom: 40px;
    }

    .home-card h3 {
        color: #003366;
    }

    .home-card p {
        font-size: 1rem;
        color: #555;
    }

    .home-card .btn {
        margin-top: 20px;
    }
</style>
</head>
<body>

    <nav class="navbar navbar-expand-lg navbar-light">
        <a class="navbar-brand" href="home.php">Tiket Pesawat</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" href="home.php">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="index.php">Daftar Tiket Pesawat</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="contact.php">Kontak</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link logout-btn" href="javascript:logout()">Logout</a>
                </li>
            </ul>
        </div>
    </nav>

    <div class="container">
        <div class="home-content">
            <h1>Selamat Datang di Tiket Pesawat</h1>
            <h2>Temukan tiket pesawat Anda dengan mudah!</h2>
            <p>Di sini Anda dapat mencari tiket pesawat ke berbagai destinasi, melihat informasi tentang jadwal dan harga tiket, dan melakukan pemesanan dengan cepat.</p>
        </div>

        <div class="home-card">
            <h3>Kenapa Memilih Tiket Pesawat?</h3>
            <p>Website kami menyediakan pilihan tiket pesawat yang lengkap dan terpercaya. Dapatkan harga terbaik untuk tiket penerbangan Anda, serta kemudahan dalam memilih jadwal yang sesuai dengan kebutuhan Anda.</p>
            <a href="index.php" class="btn btn-primary">Lihat Daftar Tiket Pesawat</a>
        </div>

        <div class="home-card">
            <h3>Kontak Kami</h3>
            <p>Jika Anda memiliki pertanyaan atau membutuhkan bantuan lebih lanjut, jangan ragu untuk menghubungi kami melalui halaman kontak.</p>
            <a href="contact.php" class="btn btn-primary">Hubungi Kami</a>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        // Optional logout function, you can add your logout logic here
        function logout() {
            alert("You have logged out.");
            // Redirect or other logout actions
        }
    </script>
</body>
</html>
```
### contact.php ###
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kontak - Daftar Tiket Pesawat</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #f0f8ff; /* Light Blue untuk halaman Kontak, disesuaikan dengan index */
            margin-top: 50px;
        }
        
        .navbar {
            background-color:  #00509e;
            position: fixed;
            width: 100%;
            top: 0;
            z-index: 1000;
            padding: 10px 0;
        }
        
        .navbar-brand {
            font-size: 1.8rem;
            font-weight: bold;
            color: white !important;
        }
        
        .navbar-nav .nav-link {
            color: white !important;
            font-size: 1.1rem;
            padding: 10px 20px;
            transition: background-color 0.3s ease;
        }
        
        .navbar-nav .nav-link:hover {
            color: #ffd700 !important;
            background-color: #003366;
            border-radius: 5px;
        }

        .contact-section {
            margin-top: 100px;
            padding: 60px 0;
            background-color: #f0f8ff; /* Light Blue Background */
        }

        .contact-section h1 {
            text-align: center;
            font-size: 2.5rem;
            margin-bottom: 20px;
        }

        .contact-info {
            text-align: center;
            font-size: 1.2rem;
            color: #6c757d;
            margin-bottom: 40px;
        }

        .container .row .col-md-4 h3 {
            font-size: 1.5rem;
            margin-bottom: 10px;
        }

        .container .row .col-md-4 p {
            font-size: 1.1rem;
            color: #333;
        }

        .form-section {
            background-color: #fff;
            padding: 40px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        .form-section h3 {
            text-align: center;
            font-size: 2rem;
            margin-bottom: 30px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .submit-btn {
            display: block;
            width: 100%;
            background-color: #006400;
            color: white;
            padding: 10px;
            font-size: 1.2rem;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        .submit-btn:hover {
            background-color: #228b22;
        }

        .footer {
            background-color: #f1f1f1;
            padding: 20px;
            text-align: center;
        }
    </style>
</head>
<body>

    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-light">
        <a class="navbar-brand" href="home.php">Daftar Tiket Pesawat</a>
        <div class="collapse navbar-collapse">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link" href="home.php">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="index.php">Daftar Tiket</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link active" href="contact.php">Kontak</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link logout-btn" href="login.php" onclick="logout()">Logout</a>
                </li>
            </ul>
        </div>
    </nav>

    <!-- Contact Section -->
    <div class="contact-section">
        <h1>Kontak Kami</h1>
        <p class="contact-info">Jika Anda memiliki pertanyaan atau ingin mengetahui lebih lanjut tentang tiket event, silakan hubungi kami atau gunakan formulir untuk mengirim pesan.</p>

        <!-- Contact Information -->
        <div class="container">
            <div class="row">
                <div class="col-md-4">
                    <h3>Alamat</h3>
                    <p>Jl. Event No.456, Bandung, Indonesia</p>
                </div>
                <div class="col-md-4">
                    <h3>Email</h3>
                    <p>info@daftartiket.com</p>
                </div>
                <div class="col-md-4">
                    <h3>Telepon</h3>
                    <p>(022) 9876-5432</p>
                </div>
            </div>
        </div>

        <!-- Contact Form -->
        <div class="container form-section">
            <h3>Kirim Pesan</h3>
            <form method="post" action="submit_contact.php" onsubmit="confirmSubmission(event)">
                <div class="form-group">
                    <input type="text" class="form-control" id="name" name="name" placeholder="Nama" required>
                </div>
                <div class="form-group">
                    <input type="email" class="form-control" id="email" name="email" placeholder="Email" required>
                </div>
                <div class="form-group">
                    <textarea class="form-control" id="message" name="message" rows="4" placeholder="Pesan" required></textarea>
                </div>
                <button type="submit" class="btn submit-btn">Kirim Pesan</button>
            </form>
        </div>
    </div>

    <!-- Footer Section -->
    <div class="footer">
        <p>&copy; 2025 Daftar Tiket Pesawat. All Rights Reserved.</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        function logout() {
            alert('Anda telah keluar');
            window.location.href = 'login.php';
        }

        function confirmSubmission(event) {
            event.preventDefault();
            alert('Pesan Anda telah berhasil dikirim. Terima kasih!');
            window.location.href = 'contact.php';
        }
    </script>

</body>
</html>
```
### logout.php ###
```
<?php
session_start();  // Mulai sesi jika belum dimulai

// Hapus semua variabel sesi
session_unset();

// Hapus sesi itu sendiri
session_destroy();

// Kirimkan respons sukses
echo json_encode(['success' => true]);
?>
```

### tickets_api.php ###
```
<?php
header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Methods: GET, POST, PUT, DELETE");
header("Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");

$method = $_SERVER['REQUEST_METHOD'];
$request = [];

if (isset($_SERVER['PATH_INFO'])) {
    $request = explode('/', trim($_SERVER['PATH_INFO'], '/'));
}

function getConnection() {
    $host = 'localhost';
    $db   = 'ticketstore';
    $user = 'root';
    $pass = ''; // Ganti dengan password MySQL Anda jika ada
    $charset = 'utf8mb4';

    $dsn = "mysql:host=$host;dbname=$db;charset=$charset";
    $options = [
        PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES   => false,
    ];
    try {
        return new PDO($dsn, $user, $pass, $options);
    } catch (\PDOException $e) {
        throw new \PDOException($e->getMessage(), (int)$e->getCode());
    }
}

function response($status, $data = NULL) {
    header("HTTP/1.1 " . $status);
    if ($data) {
        echo json_encode($data);
    }
    exit();
}

$db = getConnection();

switch ($method) {
    case 'GET':
        if (!empty($request) && isset($request[0])) {
            $id = $request[0];
            $stmt = $db->prepare("SELECT * FROM tickets WHERE id = ?");
            $stmt->execute([$id]);
            $ticket = $stmt->fetch();
            if ($ticket) {
                response(200, $ticket);
            } else {
                response(404, ["message" => "Ticket not found"]);
            }
        } else {
            $stmt = $db->query("SELECT * FROM tickets");
            $tickets = $stmt->fetchAll();
            response(200, $tickets);
        }
        break;
    
    case 'POST':
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->destination) || !isset($data->date) || !isset($data->price) || !isset($data->stock)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "INSERT INTO tickets (destination, date, price, stock) VALUES (?, ?, ?, ?)";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->destination, $data->date, $data->price, $data->stock])) {
            response(201, ["message" => "Ticket created", "id" => $db->lastInsertId()]);
        } else {
            response(500, ["message" => "Failed to create ticket"]);
        }
        break;
    
    case 'PUT':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "Ticket ID is required"]);
        }
        $id = $request[0];
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->destination) || !isset($data->date) || !isset($data->price) || !isset($data->stock)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "UPDATE tickets SET destination = ?, date = ?, price = ?, stock = ? WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->destination, $data->date, $data->price, $data->stock, $id])) {
            response(200, ["message" => "Ticket updated"]);
        } else {
            response(500, ["message" => "Failed to update ticket"]);
        }
        break;

    case 'DELETE':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "Ticket ID is required"]);
        }
        $id = $request[0];
        $sql = "DELETE FROM tickets WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$id])) {
            response(200, ["message" => "Ticket deleted"]);
        } else {
            response(500, ["message" => "Failed to delete ticket"]);
        }
        break;
    
    default:
        response(405, ["message" => "Method not allowed"]);
        break;
}
?>

```
