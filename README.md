using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq; 
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace HR_Management
{
    public partial class Form1 : Form
    {
        List<Employee> employees = new List<Employee>();
        TextBox txtNama, txtPosisi, txtGaji;
        DataGridView dgv;
        Panel pnlMainWorkspace; // Deklarasi 

        public class Employee
        {
            public string Nama { get; set; }
            public string Posisi { get; set; }
            public int Gaji { get; set; }
        }

        public Form1()
        {
            InitializeComponent();
            BuatUI();
        }
        // --- HALAMAN ABSENSI ---
        public class Attendance
        {
            public string Tanggal { get; set; }
            public string Nama { get; set; }
            public string JamMasuk { get; set; }
            public string Status { get; set; }
        }
        private Panel BuatHalamanAbsensi()
        {
            Panel pnl = new Panel { Dock = DockStyle.Fill, BackColor = Color.FromArgb(244, 247, 252), Padding = new Padding(30) };

            Label lbl = new Label
            {
                Text = "Log Absensi Real-time",
                Font = new Font("Segoe UI", 16, FontStyle.Bold),
                Location = new Point(30, 20),
                AutoSize = true,
                ForeColor = Color.FromArgb(44, 62, 80)
            };

            // --- LOGIKA MENGISI DATA ---
            List<Attendance> attendanceLog = new List<Attendance>();
            string tglSekarang = DateTime.Now.ToString("dd MMM yyyy");

            if (employees.Count == 0)
            {
                // Data dummy jika list karyawan masih kosong agar portofolio tidak terlihat sepi
                attendanceLog.Add(new Attendance { Tanggal = tglSekarang, Nama = "Contoh Karyawan", JamMasuk = "08:00", Status = "Hadir" });
            }
            else
            {
                foreach (var emp in employees)
                {
                    attendanceLog.Add(new Attendance
                    {
                        Tanggal = tglSekarang,
                        Nama = emp.Nama,
                        JamMasuk = "08:" + new Random().Next(10, 59), // Simulasi jam masuk acak
                        Status = "Hadir"
                    });
                }
            }

            // --- STYLING DATAGRIDVIEW (Apple-ish Style) ---
            DataGridView dgvAbsen = new DataGridView
            {
                Location = new Point(30, 70),
                Size = new Size(800, 450),
                BackgroundColor = Color.White,
                BorderStyle = BorderStyle.None,
                DataSource = attendanceLog,
                AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill,
                RowHeadersVisible = false,
                AllowUserToAddRows = false,
                ReadOnly = true,
                SelectionMode = DataGridViewSelectionMode.FullRowSelect,
                EnableHeadersVisualStyles = false
            };

            // Header Styling
            dgvAbsen.ColumnHeadersHeight = 40;
            dgvAbsen.ColumnHeadersDefaultCellStyle.BackColor = Color.FromArgb(52, 152, 219);
            dgvAbsen.ColumnHeadersDefaultCellStyle.ForeColor = Color.White;
            dgvAbsen.ColumnHeadersDefaultCellStyle.Font = new Font("Segoe UI", 10, FontStyle.Bold);

            // Cell Styling
            dgvAbsen.DefaultCellStyle.Font = new Font("Segoe UI", 9);
            dgvAbsen.DefaultCellStyle.SelectionBackColor = Color.FromArgb(235, 245, 251);
            dgvAbsen.DefaultCellStyle.SelectionForeColor = Color.FromArgb(44, 62, 80);
            dgvAbsen.AlternatingRowsDefaultCellStyle.BackColor = Color.FromArgb(250, 250, 250);

            pnl.Controls.Add(lbl);
            pnl.Controls.Add(dgvAbsen);

            return pnl;
        }
        private Panel BuatHalamanDashboard()
        {
            Panel pnl = new Panel { Dock = DockStyle.Fill, BackColor = Color.FromArgb(244, 247, 252), Padding = new Padding(20) };

            // --- Judul Dashboard ---
            Label lblTitle = new Label
            {
                Text = "Main Dashboard Analysis",
                Font = new Font("Segoe UI", 16, FontStyle.Bold),
                Location = new Point(30, 20),
                AutoSize = true,
                ForeColor = Color.FromArgb(44, 62, 80)
            };
            pnl.Controls.Add(lblTitle);

            // --- STAT CARDS (Angka Ringkasan) ---
            pnl.Controls.Add(BuatStatCard("Total Karyawan", employees.Count.ToString(), "Orang", Color.FromArgb(52, 152, 219), 30, 70));
            pnl.Controls.Add(BuatStatCard("Total Budget Gaji", "Rp " + employees.Sum(x => (long)x.Gaji).ToString("N0"), "Estimasi", Color.FromArgb(46, 204, 113), 280, 70));
            pnl.Controls.Add(BuatStatCard("Rata-rata Gaji", "Rp " + (employees.Count > 0 ? employees.Average(x => x.Gaji) : 0).ToString("N0"), "Per Karyawan", Color.FromArgb(155, 89, 182), 530, 70));

            // --- AREA GRAFIK (Simulasi Visual) ---
            GroupBox gbGraph = new GroupBox
            {
                Text = " Pertumbuhan SDM & Tren Gaji ",
                Size = new Size(750, 300),
                Location = new Point(30, 220),
                BackColor = Color.White,
                Font = new Font("Segoe UI", 9, FontStyle.Bold)
            };
            pnl.Controls.Add(gbGraph);

            // Tambahkan Label Simulasi Grafik (Karena WinForms Chart butuh setup library)
            Label lblChartSim = new Label
            {
                Text = " [ Area Visualisasi Data: Grafik Trend Gaji & Clustering ] \n\n" +
                       "Data ini akan terupdate secara real-time berdasarkan input di Menu Karyawan.",
                TextAlign = ContentAlignment.MiddleCenter,
                Dock = DockStyle.Fill,
                ForeColor = Color.Gray,
                Font = new Font("Segoe UI", 10, FontStyle.Italic)
            };
            gbGraph.Controls.Add(lblChartSim);

            return pnl;
        }
        private Panel BuatStatCard(string title, string value, string desc, Color accentColor, int x, int y)
        {
            Panel card = new Panel
            {
                Size = new Size(230, 120),
                Location = new Point(x, y),
                BackColor = Color.White,
                BorderStyle = BorderStyle.None
            };

            // Efek Border Bawah untuk warna
            Panel pnlBorder = new Panel { Dock = DockStyle.Bottom, Height = 5, BackColor = accentColor };
            card.Controls.Add(pnlBorder);

            Label lblT = new Label { Text = title, Location = new Point(15, 15), AutoSize = true, ForeColor = Color.Gray, Font = new Font("Segoe UI", 9) };
            Label lblV = new Label { Text = value, Location = new Point(15, 40), AutoSize = true, ForeColor = Color.Black, Font = new Font("Segoe UI", 15, FontStyle.Bold) };
            Label lblD = new Label { Text = desc, Location = new Point(15, 75), AutoSize = true, ForeColor = Color.Silver, Font = new Font("Segoe UI", 8) };

            card.Controls.Add(lblT);
            card.Controls.Add(lblV);
            card.Controls.Add(lblD);

            return card;
        }

        // --- HALAMAN PENGGAJIAN ---
        private Panel BuatHalamanGaji()
        {
            Panel pnl = new Panel { Dock = DockStyle.Fill, BackColor = Color.White, Padding = new Padding(30) };

            Label lbl = new Label
            {
                Text = "Payroll / Input Slip Gaji",
                Font = new Font("Segoe UI", 16, FontStyle.Bold),
                Location = new Point(30, 20),
                AutoSize = true
            };
            pnl.Controls.Add(lbl);

            // --- GROUPBOX INPUT PAYROLL ---
            GroupBox gbPayroll = new GroupBox
            {
                Text = " Detail Komponen Gaji ",
                Size = new Size(800, 250),
                Location = new Point(30, 70),
                Font = new Font("Segoe UI", 9, FontStyle.Bold)
            };
            pnl.Controls.Add(gbPayroll);

            // Baris 1: Pilih Karyawan
            gbPayroll.Controls.Add(new Label { Text = "Pilih Karyawan", Location = new Point(20, 40), AutoSize = true });
            ComboBox cbKaryawan = new ComboBox { Location = new Point(20, 60), Width = 250, DropDownStyle = ComboBoxStyle.DropDownList };
            foreach (var emp in employees) { cbKaryawan.Items.Add(emp.Nama); } // Ambil dari list karyawan
            gbPayroll.Controls.Add(cbKaryawan);

            // Baris 2: Bonus & Potongan
            gbPayroll.Controls.Add(new Label { Text = "Bonus (Rp)", Location = new Point(300, 40), AutoSize = true });
            TextBox txtBonus = new TextBox { Location = new Point(300, 60), Width = 200 };
            gbPayroll.Controls.Add(txtBonus);

            gbPayroll.Controls.Add(new Label { Text = "Potongan / Kasbon (Rp)", Location = new Point(530, 40), AutoSize = true });
            TextBox txtPotongan = new TextBox { Location = new Point(530, 60), Width = 200 };
            gbPayroll.Controls.Add(txtPotongan);

            // Baris 3: Keterangan
            gbPayroll.Controls.Add(new Label { Text = "Keterangan Tambahan", Location = new Point(20, 100), AutoSize = true });
            TextBox txtKet = new TextBox { Location = new Point(20, 120), Width = 710, Multiline = true, Height = 50 };
            gbPayroll.Controls.Add(txtKet);

            // Tombol Hitung & Simpan
            Button btnGenerate = new Button
            {
                Text = "CETAK SLIP GAJI",
                Location = new Point(20, 190),
                Size = new Size(180, 40),
                BackColor = Color.FromArgb(52, 152, 219),
                ForeColor = Color.White,
                FlatStyle = FlatStyle.Flat,
                Cursor = Cursors.Hand
            };

            // Logika saat tombol cetak diklik
            btnGenerate.Click += (s, e) => {
                if (cbKaryawan.SelectedIndex == -1)
                {
                    MessageBox.Show("Silakan pilih karyawan terlebih dahulu!");
                    return;
                }

                string nama = cbKaryawan.SelectedItem.ToString();
                var empData = employees.FirstOrDefault(x => x.Nama == nama);

                int bonus = int.TryParse(txtBonus.Text, out int b) ? b : 0;
                int potongan = int.TryParse(txtPotongan.Text, out int p) ? p : 0;
                int totalGaji = empData.Gaji + bonus - potongan;

                MessageBox.Show($"SLIP GAJI BERHASIL DIBUAT\n\n" +
                                $"Nama: {nama}\n" +
                                $"Gaji Pokok: Rp{empData.Gaji:N0}\n" +
                                $"Bonus: Rp{bonus:N0}\n" +
                                $"Potongan: Rp{potongan:N0}\n" +
                                $"-----------------------------\n" +
                                $"TOTAL DITERIMA: Rp{totalGaji:N0}",
                                "Payroll System", MessageBoxButtons.OK, MessageBoxIcon.Information);
            };

            gbPayroll.Controls.Add(btnGenerate);

            return pnl;
        }

        // --- HALAMAN LAPORAN ---
        private Panel BuatHalamanLaporan()
        {
            Panel pnl = new Panel { Dock = DockStyle.Fill, BackColor = Color.White, Padding = new Padding(30) };

            Label lbl = new Label
            {
                Text = "Laporan SDM & Pengeluaran Gaji",
                Font = new Font("Segoe UI", 16, FontStyle.Bold),
                Location = new Point(30, 20),
                AutoSize = true
            };
            pnl.Controls.Add(lbl);

            // --- PANEL FILTER PERIODE ---
            GroupBox gbFilter = new GroupBox
            {
                Text = " Pilih Periode Laporan ",
                Size = new Size(800, 100),
                Location = new Point(30, 70),
                Font = new Font("Segoe UI", 9, FontStyle.Bold)
            };
            pnl.Controls.Add(gbFilter);

            gbFilter.Controls.Add(new Label { Text = "Dari Tanggal:", Location = new Point(20, 35), AutoSize = true });
            DateTimePicker dtpDari = new DateTimePicker
            {
                Location = new Point(20, 55),
                Width = 200,
                Format = DateTimePickerFormat.Short
            };
            gbFilter.Controls.Add(dtpDari);

            gbFilter.Controls.Add(new Label { Text = "Sampai Tanggal:", Location = new Point(250, 35), AutoSize = true });
            DateTimePicker dtpSampai = new DateTimePicker
            {
                Location = new Point(250, 55),
                Width = 200,
                Format = DateTimePickerFormat.Short
            };
            gbFilter.Controls.Add(dtpSampai);

            Button btnCetak = new Button
            {
                Text = "GENERATE LAPORAN",
                Location = new Point(480, 50),
                Size = new Size(180, 35),
                BackColor = Color.FromArgb(44, 62, 80),
                ForeColor = Color.White,
                FlatStyle = FlatStyle.Flat,
                Cursor = Cursors.Hand
            };
            gbFilter.Controls.Add(btnCetak);

            // --- AREA PREVIEW RINGKASAN ---
            Panel pnlPreview = new Panel
            {
                Size = new Size(800, 300),
                Location = new Point(30, 190),
                BorderStyle = BorderStyle.FixedSingle,
                BackColor = Color.FromArgb(250, 250, 250)
            };
            pnl.Controls.Add(pnlPreview);

            Label lblPreview = new Label
            {
                Text = "Klik 'Generate Laporan' untuk melihat ringkasan periode.",
                TextAlign = ContentAlignment.MiddleCenter,
                Dock = DockStyle.Fill,
                ForeColor = Color.Gray
            };
            pnlPreview.Controls.Add(lblPreview);

            // Logika Klik Cetak/Generate
            btnCetak.Click += (s, e) => {
                int totalKaryawan = employees.Count;
                long totalGaji = employees.Sum(x => (long)x.Gaji);

                lblPreview.TextAlign = ContentAlignment.TopLeft;
                lblPreview.Padding = new Padding(20);
                lblPreview.Text = $"LAPORAN SUMBER DAYA MANUSIA\n" +
                                  $"Periode: {dtpDari.Value.ToShortDateString()} - {dtpSampai.Value.ToShortDateString()}\n" +
                                  $"------------------------------------------------------------\n\n" +
                                  $"1. Total Karyawan Aktif: {totalKaryawan} Orang\n" +
                                  $"2. Total Pengeluaran Gaji Pokok: Rp{totalGaji:N0}\n" +
                                  $"3. Rata-rata Gaji: Rp{(totalKaryawan > 0 ? totalGaji / totalKaryawan : 0):N0}\n\n" +
                                  $"* Laporan ini dibuat secara otomatis oleh sistem pada {DateTime.Now}";

                MessageBox.Show("Laporan periode " + dtpDari.Value.ToString("MMM yyyy") + " berhasil dibuat!", "Success");
            };

            return pnl;
        }
        private void BuatUI()
        {
            this.Text = "HR Enterprise System v1.0";
            this.Size = new Size(1100, 700);
            this.StartPosition = FormStartPosition.CenterScreen;

            // --- 1. SIDEBAR PANEL ---
            Panel pnlSidebar = new Panel
            {
                Dock = DockStyle.Left,
                Width = 240,
                BackColor = Color.FromArgb(34, 45, 50)
            };
            this.Controls.Add(pnlSidebar);

            Label lblLogo = new Label
            {
                Text = "HR MASTER",
                ForeColor = Color.White,
                Font = new Font("Segoe UI", 16, FontStyle.Bold),
                Location = new Point(20, 25),
                AutoSize = true
            };
            pnlSidebar.Controls.Add(lblLogo);

            string[] menus = { "Dashboard", "Data Karyawan", "Absensi", "Penggajian", "Laporan" };
            for (int i = 0; i < menus.Length; i++)
            {
                Button btnMenu = new Button
                {
                    Text = "        " + menus[i],
                    Size = new Size(240, 50),
                    Location = new Point(0, 100 + (i * 50)),
                    FlatStyle = FlatStyle.Flat,
                    ForeColor = Color.FromArgb(191, 203, 217),
                    TextAlign = ContentAlignment.MiddleLeft,
                    Font = new Font("Segoe UI", 10),
                    Cursor = Cursors.Hand
                };
                btnMenu.FlatAppearance.BorderSize = 0;
                btnMenu.Click += (s, e) => {
                    string menuText = ((Button)s).Text.Trim();
                    if (menuText == "Dashboard") TampilkanHalaman(BuatHalamanDashboard());
                    else if (menuText == "Data Karyawan") TampilkanHalaman(BuatHalamanKaryawan());
                };
                pnlSidebar.Controls.Add(btnMenu);
                btnMenu.Click += (s, e) => {
                    string menuText = ((Button)s).Text.Trim();

                    if (menuText == "Dashboard")
                        TampilkanHalaman(BuatHalamanDashboard());
                    else if (menuText == "Data Karyawan")
                        TampilkanHalaman(BuatHalamanKaryawan());
                    else if (menuText == "Absensi")
                        TampilkanHalaman(BuatHalamanAbsensi());
                    else if (menuText == "Penggajian")
                        TampilkanHalaman(BuatHalamanGaji());
                    else if (menuText == "Laporan")
                        TampilkanHalaman(BuatHalamanLaporan());
                };
            }

            // --- 2. HEADER PANEL ---
            Panel pnlHeader = new Panel
            {
                Dock = DockStyle.Top,
                Height = 70,
                BackColor = Color.White,
                BorderStyle = BorderStyle.FixedSingle
            };
            this.Controls.Add(pnlHeader);

            Label lblTitle = new Label
            {
                Text = "EmployeeHub",
                Font = new Font("Segoe UI", 14, FontStyle.Bold),
                Location = new Point(20, 22),
                AutoSize = true,
                ForeColor = Color.FromArgb(44, 62, 80)
            };
            pnlHeader.Controls.Add(lblTitle);

            // --- 3. MAIN WORKSPACE ---
            pnlMainWorkspace = new Panel
            {
                Dock = DockStyle.Fill,
                BackColor = Color.FromArgb(244, 247, 252)
            };
            this.Controls.Add(pnlMainWorkspace);
            pnlMainWorkspace.BringToFront();

            // Tampilkan Dashboard sebagai awal
            TampilkanHalaman(BuatHalamanDashboard());
        }

        

        private Panel BuatHalamanKaryawan()
        {
            Panel pnl = new Panel { Dock = DockStyle.Fill };

            // GroupBox Input
            GroupBox gbInput = new GroupBox
            {
                Text = " Form Tambah Karyawan ",
                Size = new Size(800, 180),
                Location = new Point(30, 20),
                Font = new Font("Segoe UI", 9, FontStyle.Bold),
                BackColor = Color.White
            };

            gbInput.Controls.Add(new Label { Text = "Nama Lengkap", Location = new Point(25, 40), AutoSize = true });
            txtNama = new TextBox { Location = new Point(25, 65), Width = 230, Font = new Font("Segoe UI", 10) };
            gbInput.Controls.Add(txtNama);

            gbInput.Controls.Add(new Label { Text = "Jabatan", Location = new Point(280, 40), AutoSize = true });
            txtPosisi = new TextBox { Location = new Point(280, 65), Width = 200, Font = new Font("Segoe UI", 10) };
            gbInput.Controls.Add(txtPosisi);

            gbInput.Controls.Add(new Label { Text = "Gaji (Rp)", Location = new Point(505, 40), AutoSize = true });
            txtGaji = new TextBox { Location = new Point(505, 65), Width = 180, Font = new Font("Segoe UI", 10) };
            gbInput.Controls.Add(txtGaji);

            Button btnSimpan = new Button
            {
                Text = "SIMPAN DATA",
                Location = new Point(25, 115),
                Size = new Size(150, 40),
                BackColor = Color.FromArgb(39, 174, 96),
                ForeColor = Color.White,
                FlatStyle = FlatStyle.Flat,
                Font = new Font("Segoe UI", 9, FontStyle.Bold),
                Cursor = Cursors.Hand
            };
            btnSimpan.FlatAppearance.BorderSize = 0;
            btnSimpan.Click += BtnTambah_Click;
            gbInput.Controls.Add(btnSimpan);

            // Tabel
            dgv = new DataGridView
            {
                Location = new Point(30, 220),
                Size = new Size(800, 350),
                BackgroundColor = Color.White,
                BorderStyle = BorderStyle.None,
                AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill,
                RowHeadersVisible = false,
                SelectionMode = DataGridViewSelectionMode.FullRowSelect,
                AllowUserToAddRows = false,
                EnableHeadersVisualStyles = false,
                DataSource = employees // Langsung sambungkan ke list
            };

            dgv.ColumnHeadersHeight = 45;
            dgv.ColumnHeadersDefaultCellStyle.BackColor = Color.FromArgb(52, 73, 94);
            dgv.ColumnHeadersDefaultCellStyle.ForeColor = Color.White;
            dgv.ColumnHeadersDefaultCellStyle.Font = new Font("Segoe UI", 10, FontStyle.Bold);

            pnl.Controls.Add(gbInput);
            pnl.Controls.Add(dgv);
            return pnl;
        }

        private void TampilkanHalaman(Control halamanBaru)
        {
            pnlMainWorkspace.Controls.Clear();
            halamanBaru.Dock = DockStyle.Fill;
            pnlMainWorkspace.Controls.Add(halamanBaru);
        }

        private void BtnTambah_Click(object sender, EventArgs e)
        {
            if (string.IsNullOrWhiteSpace(txtNama.Text) || string.IsNullOrWhiteSpace(txtPosisi.Text) || string.IsNullOrWhiteSpace(txtGaji.Text))
            {
                MessageBox.Show("Data tidak boleh kosong!");
                return;
            }

            int gajiNominal = 0;
            int.TryParse(txtGaji.Text, out gajiNominal);

            employees.Add(new Employee { Nama = txtNama.Text, Posisi = txtPosisi.Text, Gaji = gajiNominal });

            dgv.DataSource = null;
            dgv.DataSource = employees;

            txtNama.Clear(); txtPosisi.Clear(); txtGaji.Clear();
            txtNama.Focus();
        }
    }
}
