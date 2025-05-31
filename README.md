#include <iostream>
#include <vector>
#include <string>
#include <iomanip>
#include <map>
#include <algorithm>
#include <ctime>
using namespace std;

struct Khachhang {
    string phone;
    string name;
};

struct San {
    int id;
    float giasan;
    bool trangThai; // true: đã đặt, false: còn trống
    string thoiGianDat;
    string ngayDat;
};

struct Phukien {
    string tenphukien;
    float giaphukien;
    int soluong;
};

struct Cafe {
    string tenmon;
    float giamon;
};

class Hoadon {
public:
    string ngay;
    float tongtien;
    vector<pair<string, int>> danhSachMon;
};

struct Lichdatsan : Hoadon {
    int idSan;
    string phoneKhach;
    string thoiGian;
};

vector<Khachhang> danhSachKhach;
vector<San> danhSachSan;
vector<Phukien> khoPhuKien;
vector<Cafe> menuCafe;
vector<Hoadon> hoaDonCafe;
vector<Lichdatsan> lichDat;

// Hàm khởi tạo dữ liệu
void khoiTaoDuLieu() {
    danhSachSan.clear();
    for (int i = 1; i <= 5; i++) {
        San san;
        san.id = i;
        san.giasan = 150000;
        san.trangThai = false;
        san.thoiGianDat = "";
        san.ngayDat = "";
        danhSachSan.push_back(san);
    }

danhSachKhach.clear();
Khachhang kh;
kh.name = "Tran Van Duong";
kh.phone = "0967951155";
danhSachKhach.push_back(kh);

khoPhuKien.clear();
khoPhuKien.push_back({ "Vot", 1500000, 10 });
khoPhuKien.push_back({ "Bong", 50000, 50 });
khoPhuKien.push_back({ "Tat", 100000, 20 });
khoPhuKien.push_back({ "Quan can", 12000, 50 });

menuCafe.clear();
menuCafe.push_back({ "Capuchino Assasino", 200000});
menuCafe.push_back({ "Ca phe muoi", 22000});
menuCafe.push_back({ "Nuoc ep ca rot", 20000});
menuCafe.push_back({ "Soda chanh", 18000});
menuCafe.push_back({ "Sua chua viet quat", 18000});
menuCafe.push_back({ "Matcha Latte Oatside", 30000});

hoaDonCafe.clear();
lichDat.clear();
}

// Hàm kiểm tra khách hàng có tồn tại không
bool kiemTraKhachHang(string phone) {
    for (const auto& kh : danhSachKhach) {
        if (kh.phone == phone) {
            return true;
        }
    }
    return false;
}

// Hàm thêm khách hàng mới
void themKhachHang() {
    Khachhang kh;
    cout << "Nhap so dien thoai: ";
    cin >> kh.phone;
    cin.ignore();
    cout << "Nhap ten khach hang: ";
    getline(cin, kh.name);
    danhSachKhach.push_back(kh);
    cout << "Da them khach hang thanh cong!" << endl;
}

// Hàm xem trạng thái sân
void xemTrangThaiSan() {
    string ngay;
    cout << "Nhap ngay can xem (dd/mm/yyyy): ";
    cin >> ngay;

    cout << "\n===== TRANG THAI SAN NGAY " << ngay << " =====" << endl;
    cout << setw(10) << "So san" << setw(15) << "Gia thue" << setw(20) << "Trang thai" << setw(20) << "Thoi gian dat" << endl;

    for (const auto& san : danhSachSan) {
            cout << setw(10) << san.id << setw(15) << san.giasan << setw(20) << (san.trangThai ? "Da dat" : "Con trong") << setw(20) << (san.trangThai ? san.thoiGianDat : "") << endl;
        }
    }


// Hàm đặt sân
void datSan() {
    Lichdatsan lds;
    cout << "Nhap ngay dat (dd/mm/yyyy): ";
    cin >> lds.ngay;
    cout << "Nhap thoi gian dat (hh:mm-hh:mm): ";
    cin >> lds.thoiGian;
    cout << "Nhap so dien thoai khach hang: ";
    cin >> lds.phoneKhach;

    if (!kiemTraKhachHang(lds.phoneKhach)) {
        cout << "Khach hang chua ton tai. Vui long them moi!" << endl;
        themKhachHang();
    }

    cout << "Danh sach san hien co:" << endl;
    xemTrangThaiSan();

    cout << "Chon ID san muon dat: ";
    cin >> lds.idSan;

    // Kiểm tra sân có tồn tại không
    bool sanTonTai = false;
    for (auto& san : danhSachSan) {
        if (san.id == lds.idSan) {
            sanTonTai = true;
            if (san.trangThai && san.ngayDat == lds.ngay && san.thoiGianDat == lds.thoiGian) {
                cout << "San da duoc dat vao thoi gian nay!" << endl;
                return;
            }
            san.trangThai = true;
            san.ngayDat = lds.ngay;
            san.thoiGianDat = lds.thoiGian;
            break;
        }
    }

    if (!sanTonTai) {
        cout << "ID san khong ton tai!" << endl;
        return;
    }

    // Tính tổng tiền
    for (const auto& san : danhSachSan) {
        if (san.id == lds.idSan) {
            lds.tongtien = san.giasan;
            break;
        }
    }

    lichDat.push_back(lds);
    cout << "Dat san thanh cong!" << endl;
}

// Hàm hủy đặt sân
void huyDatSan() {
    string phone;
    string ngay;
    cout << "Nhap so dien thoai khach hang: ";
    cin >> phone;
    cout << "Nhap ngay dat (dd/mm/yyyy): ";
    cin >> ngay;

    bool found = false;
    for (auto it = lichDat.begin(); it != lichDat.end(); ) {
        if (it->phoneKhach == phone && it->ngay == ngay) {
            // Cập nhật trạng thái sân
            for (auto& san : danhSachSan) {
                if (san.id == it->idSan && san.ngayDat == ngay) {
                    san.trangThai = false;
                    san.thoiGianDat = "";
                    san.ngayDat = "";
                    break;
                }
            }
            it = lichDat.erase(it);
            found = true;
            cout << "Da huy dat san thanh cong!" << endl;
        }
        else {
            ++it;
        }
    }

    if (!found) {
        cout << "Khong tim thay lich dat phong!" << endl;
    }
}

// Hàm hiển thị danh sách khách hàng
void hienThiDanhSachKhachHang() {
    cout << "\n===== DANH SACH KHACH HANG =====" << endl;
    cout << setw(15) << "So DT" << setw(25) << "Ten khach hang" << endl;
    for (const auto& kh : danhSachKhach) {
        cout << setw(15) << kh.phone << setw(25) << kh.name << endl;
    }
}

// Hàm nhập hàng phụ kiện
void nhapHang() {
    Phukien pk;
    cout << "Nhap ten phu kien: ";
    cin.ignore();
    getline(cin, pk.tenphukien);
    cout << "Nhap gia phu kien: ";
    cin >> pk.giaphukien;
    cout << "Nhap so luong: ";
    cin >> pk.soluong;

    // Kiểm tra xem phụ kiện đã có trong kho chưa
    bool tonTai = false;
    for (auto& item : khoPhuKien) {
        if (item.tenphukien == pk.tenphukien) {
            item.soluong += pk.soluong;
            tonTai = true;
            break;
        }
    }

    if (!tonTai) {
        khoPhuKien.push_back(pk);
    }

    cout << "Nhap hang thanh cong!" << endl;
}

// Hàm xem tồn kho
void xemTonKho() {
    cout << "\n===== TON KHO PHU KIEN =====" << endl;
    cout << setw(20) << "Ten phu kien" << setw(15) << "Gia ban" << setw(15) << "So luong" << endl;
    for (const auto& pk : khoPhuKien) {
        cout << setw(20) << pk.tenphukien
            << setw(15) << pk.giaphukien
            << setw(15) << pk.soluong << endl;
    }
}

// Hàm bán phụ kiện
void banPhuKien() {
    string tenPK;
    int soLuong;
    cout << "Nhap ten phu kien muon ban: ";
    cin.ignore();
    getline(cin, tenPK);
    cout << "Nhap so luong: ";
    cin >> soLuong;

    for (auto& pk : khoPhuKien) {
        if (pk.tenphukien == tenPK) {
            if (pk.soluong >= soLuong) {
                pk.soluong -= soLuong;
                cout << "Ban thanh cong! So luong con lai: " << pk.soluong << endl;
            }
            else {
                cout << "Khong du so luong trong kho!" << endl;
            }
            return;
        }
    }

    cout << "Khong tim thay phu kien!" << endl;
}

// Hàm cập nhật giá món
void capNhatGiaMon() {
    string tenMon;
    float giaMoi;
    cout << "Nhap ten mon can cap nhat gia: ";
    cin.ignore();
    getline(cin, tenMon);
    cout << "Nhap gia moi: ";
    cin >> giaMoi;

    for (auto& mon : menuCafe) {
        if (mon.tenmon == tenMon) {
            mon.giamon = giaMoi;
            cout << "Cap nhat gia thanh cong!" << endl;
            return;
        }
    }

    cout << "Khong tim thay mon!" << endl;
}

// Hàm hiển thị menu cafe
void hienThiMenuCafe() {
    cout << "\n===== MENU CAFE =====" << endl;
    cout << setw(20) << "Ten mon" << setw(15) << "Gia" << endl;
    for (const auto& mon : menuCafe) {
        cout << setw(20) << mon.tenmon << setw(15) << mon.giamon << endl;
    }
}

// Hàm tạo hóa đơn cafe
void taoHoaDon() {
    Hoadon hd;
    time_t now = time(0);
    tm* ltm = localtime(&now);
    hd.ngay = to_string(ltm->tm_mday) + "/" + to_string(1 + ltm->tm_mon) + "/" + to_string(1900 + ltm->tm_year);
    hd.tongtien = 0;

    char tiepTuc;
    do {
        string tenMon;
        int soLuong;
        hienThiMenuCafe();
        cout << "Nhap ten mon: ";
        cin.ignore();
        getline(cin, tenMon);
        cout << "Nhap so luong: ";
        cin >> soLuong;

        bool tonTai = false;
        for (const auto& mon : menuCafe) {
            if (mon.tenmon == tenMon) {
                hd.danhSachMon.push_back({ tenMon, soLuong });
                hd.tongtien += mon.giamon * soLuong;
                tonTai = true;
                break;
            }
        }

        if (!tonTai) {
            cout << "Mon khong ton tai trong menu!" << endl;
        }

        cout << "Ban co muon tiep tuc them mon? (y/n): ";
        cin >> tiepTuc;
    } while (tiepTuc == 'y' || tiepTuc == 'Y');

    hoaDonCafe.push_back(hd);
    cout << "Tao hoa don thanh cong! Tong tien: " << hd.tongtien << " VND" << endl;
}

// Hàm xuất hóa đơn
void xuatHoaDon() {
    if (hoaDonCafe.empty()) {
        cout << "Khong co hoa don nao!" << endl;
        return;
    }

    cout << "\n===== HOA DON =====" << endl;
    for (const auto& hd : hoaDonCafe) {
        cout << "Ngay: " << hd.ngay << endl;
        cout << setw(20) << "Ten mon" << setw(15) << "So luong" << endl;
        for (const auto& mon : hd.danhSachMon) {
            cout << setw(20) << mon.first << setw(15) << mon.second << endl;
        }
        cout << "Tong tien: " << hd.tongtien << " VND" << endl;
        cout << "------------------------" << endl;
    }
}

// Hàm thống kê doanh thu
void thongKeDoanhThu() {
    string ngay;
    cout << "Nhap ngay can thong ke (dd/mm/yyyy): ";
    cin >> ngay;

    float tongDoanhThu = 0;

    // Doanh thu từ đặt sân
    for (const auto& lds : lichDat) {
        if (lds.ngay == ngay) {
            tongDoanhThu += lds.tongtien;
        }
    }

    // Doanh thu từ cafe
    for (const auto& hd : hoaDonCafe) {
        if (hd.ngay == ngay) {
            tongDoanhThu += hd.tongtien;
        }
    }

    cout << "Tong doanh thu ngay " << ngay << ": " << tongDoanhThu << " VND" << endl;
}

// Hàm cập nhật bảng giá sân
void capNhatBangGiaSan() {
    int idSan;
    float giaMoi;
    cout << "Nhap ID san can cap nhat gia: ";
    cin >> idSan;
    cout << "Nhap gia moi: ";
    cin >> giaMoi;

    for (auto& san : danhSachSan) {
        if (san.id == idSan) {
            san.giasan = giaMoi;
            cout << "Cap nhat gia thanh cong!" << endl;
            return;
        }
    }

    cout << "Khong tim thay san!" << endl;
}

// Hàm lựa chọn chính
void luaChon() {
    int luachon1;
    do {
        cout << "\n=============MENU============" << endl;
        cout << "1. Quan ly san" << endl;
        cout << "2. Quan ly khach hang" << endl;
        cout << "3. Quan ly kho phu kien" << endl;
        cout << "4. Quan ly Menu - Cafe" << endl;
        cout << "5. Ban hang" << endl;
        cout << "6. Quan ly doanh thu" << endl;
        cout << "7. Cap nhat gia thue san" << endl;
        cout << "0. Thoat" << endl;
        cout << "Vui long chon chuc nang: ";
        cin >> luachon1;

        switch (luachon1) {
        case 1: {
            int luachon2;
            do {
                cout << "\n1. Xem trang thai san" << endl;
                cout << "2. Dat san" << endl;
                cout << "3. Huy dat san" << endl;
                cout << "0. Thoat" << endl;
                cout << "Vui long chon chuc nang: ";
                cin >> luachon2;

                switch (luachon2) {
                case 1:
                    xemTrangThaiSan();
                    break;
                case 2:
                    datSan();
                    break;
                case 3:
                    huyDatSan();
                    break;
                }
            } while (luachon2 != 0);
        } break;
        case 2:
            hienThiDanhSachKhachHang();
            break;
        case 3: {
            int luachon4;
            do {
                cout << "\n1. Nhap hang" << endl;
                cout << "2. Xem ton kho" << endl;
                cout << "3. Ban phu kien" << endl;
                cout << "0. Thoat" << endl;
                cout << "Vui long chon chuc nang: ";
                cin >> luachon4;

                switch (luachon4) {
                case 1:
                    nhapHang();
                    break;
                case 2:
                    xemTonKho();
                    break;
                case 3:
                    banPhuKien();
                    break;
                }
            } while (luachon4 != 0);
        } break;
        case 4:
            capNhatGiaMon();
            break;
        case 5: {
            int luachon6;
            do {
                cout << "\n1. Phu kien" << endl;
                cout << "2. Cafe" << endl;
                cout << "3. Xuat hoa don" << endl;
                cout << "0. Thoat" << endl;
                cout << "Vui long chon chuc nang: ";
                cin >> luachon6;

                switch (luachon6) {
                case 1:
                    banPhuKien();
                    break;
                case 2:
                    hienThiMenuCafe();
                    taoHoaDon();
                    break;
                case 3:
                    xuatHoaDon();
                    break;
                }
            } while (luachon6 != 0);
        } break;
        case 6:
            thongKeDoanhThu();
            break;
        case 7:
            capNhatBangGiaSan();
            break;
        case 0:
            cout << "Cam on ban da su dung dich vu!" << endl;
            break;
        default:
            cout << "Lua chon khong hop le!" << endl;
        }
    } while (luachon1 != 0);
}

int main() {
    khoiTaoDuLieu();
    luaChon();
    return 0;
}
