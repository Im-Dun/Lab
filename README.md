# Thuật Toán Tìm Đường A* Trong Kho Hàng

## Giới Thiệu

Dự án này cài đặt thuật toán tìm đường **A\*** (A-Star) để điều hướng robot trong một kho hàng được mô phỏng dưới dạng lưới 2 chiều (grid). Thuật toán tìm ra đường đi ngắn nhất (theo chi phí) từ điểm xuất phát đến điểm đích, đồng thời tránh các vật cản và ưu tiên đi qua các ô có chi phí thấp.

---

## Cấu Trúc Dự Án

```
Lab/
└── astar_warehouse.py   # Mã nguồn chính
```

---

## Mô Tả Bản Đồ

Bản đồ là một lưới **20 × 20** ô vuông. Mỗi ô có một loại địa hình với chi phí di chuyển tương ứng:

| Giá trị ô | Loại địa hình | Chi phí di chuyển |
|:---------:|:-------------:|:-----------------:|
| `0`       | Ô trống       | 1                 |
| `1`       | Vật cản       | Không đi được     |
| `2`       | Bùn lầy       | 3                 |
| `3`       | Đá cuội       | 5                 |

### Bố Cục Bản Đồ

- **Tường dọc**: cột 10, từ hàng 5 đến hàng 14 (`grid[5:15, 10] = 1`)
- **Tường ngang**: hàng 5, từ cột 5 đến cột 14 (`grid[5, 5:15] = 1`)
- **Vùng bùn lầy**: hàng 12–15, cột 3–7 (`grid[12:16, 3:8] = 2`)
- **Vùng đá cuội**: hàng 2–5, cột 14–17 (`grid[2:6, 14:18] = 3`)
- **Điểm xuất phát**: `(2, 2)` — góc trên bên trái
- **Điểm đích**: `(18, 18)` — góc dưới bên phải

---

## Thuật Toán A\*

Thuật toán A\* tìm đường đi có tổng chi phí nhỏ nhất dựa trên hàm đánh giá:

```
f(n) = g(n) + h(n)
```

- **g(n)**: Chi phí thực tế từ điểm xuất phát đến ô `n`.
- **h(n)**: Heuristic ước tính khoảng cách từ ô `n` đến đích (khoảng cách Manhattan).
- **f(n)**: Tổng chi phí ước tính — A\* luôn mở rộng ô có `f` nhỏ nhất trước.

Di chuyển chỉ theo **4 hướng**: lên, xuống, trái, phải (không di chuyển chéo).

---

## Kết Quả Chạy Chương Trình

Khi chạy `python astar_warehouse.py`, chương trình in ra:

```
Path found with 33 steps. Total cost = 32
```

### Giải Thích Kết Quả

- **33 bước**: Đường đi gồm 33 ô (bao gồm cả điểm xuất phát).
- **Tổng chi phí = 32**: Tổng chi phí di chuyển qua 32 bước chuyển tiếp (không tính ô xuất phát). Vì robot đi qua chủ yếu các ô trống (chi phí 1), tổng chi phí gần bằng số bước.

### Bản Đồ Trực Quan (Ký Tự)

Đường đi được đánh dấu bằng `*`:

```
00000000000000000000
00000000000000000000
00***000000000333300
0000*000000000333300
0000*000000000333300
0000*111111111333300
0000******1000000000
000000000*1000000000
000000000*1000000000
000000000*1000000000
000000000*1000000000
000000000*1000000000
000222220*1000000000
000222220*1000000000
000222220*1000000000
000222220**********0
000000000000000000*0
000000000000000000*0
000000000000000000*0
00000000000000000000
```

**Chú thích bản đồ:**
- `0` — Ô trống
- `1` — Vật cản (không đi được)
- `2` — Bùn lầy (chi phí 3)
- `3` — Đá cuội (chi phí 5)
- `*` — Đường đi của robot

### Mô Tả Đường Đi

Robot xuất phát tại góc trên bên trái `(2, 2)`, đi về phía phải một đoạn ngắn, sau đó rẽ xuống dọc theo cạnh trái của tường ngang, luồn qua khe hở giữa tường dọc, tiếp tục xuống phía dưới và cuối cùng rẽ phải đến đích `(18, 18)` ở góc dưới bên phải. Robot tránh hoàn toàn vùng bùn lầy và đá cuội để giảm chi phí di chuyển.

---

## Cách Chạy

### Yêu Cầu

```bash
pip install numpy matplotlib
```

### Thực Thi

```bash
python astar_warehouse.py
```

Chương trình sẽ in đường đi ra màn hình và hiển thị đồ thị trực quan bằng Matplotlib (điểm xanh lá = xuất phát, điểm xanh dương = đích, đường đỏ = đường đi).

---

## Cấu Trúc Mã Nguồn

| Hàm                     | Chức năng                                                      |
|-------------------------|----------------------------------------------------------------|
| `create_node()`         | Tạo một nút cho thuật toán A\*                                |
| `calculate_heuristic()` | Tính heuristic (khoảng cách Manhattan)                        |
| `get_valid_neighbors()` | Lấy danh sách các ô lân cận hợp lệ (không phải vật cản)      |
| `movement_cost()`       | Lấy chi phí di chuyển vào một ô                               |
| `find_path()`           | Thuật toán A\* chính — trả về đường đi tối ưu                 |
| `reconstruct_path()`    | Truy vết đường đi từ nút đích về nút xuất phát               |
| `visualize_path()`      | In bản đồ kết quả ra màn hình dạng ký tự                     |
| `plot_grid()`           | Vẽ bản đồ và đường đi bằng Matplotlib                        |
| `main()`                | Hàm chính — khởi tạo bản đồ và chạy thuật toán               |
