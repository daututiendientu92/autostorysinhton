# Tự động làm video người que + tự động edit video

Hai bộ công cụ chạy bằng [Claude Code](https://claude.com/claude-code), dùng để
làm video YouTube gần như không cần động tay:

| Thư mục | Làm gì |
|---|---|
| [`xuong-video-nguoi-que/`](xuong-video-nguoi-que) | Dây chuyền sản xuất video hoạt hình nhân vật que tiếng Anh 16:9, 8–10 phút — từ chọn đề tài, viết kịch bản, sinh hình, lồng giọng, dựng, tới đăng YouTube |
| [`skills/`](skills) | Hai skill cho Claude Code: `video-sinhton` (điều khiển dây chuyền trên) và `edit-viet-social` (tự edit video quay thô thành bản dựng có phụ đề + đồ hoạ) |

Đây là bộ mình dùng thật hằng ngày, không phải bản demo.

---

## Cần gì trước khi chạy

- **Python 3.10+** và **ffmpeg** (có trong PATH)
- **Claude Code** — để chạy skill
- Một nguồn sinh hình: [Muse AI](https://muse.ai) (rẻ nhất, ~50K được 1 tỷ token),
  Google Flow, hoặc Higgsfield
- Một nguồn giọng đọc: `edge-tts` (miễn phí, 322 giọng) hoặc server clone OmniVoice

```bash
pip install faster-whisper edge-tts pillow playwright requests
```

---

## Phần 1 — Xưởng video người que

Toàn bộ hướng dẫn chi tiết nằm ở hai file:

- [`QUY-TRINH-SAN-XUAT.txt`](xuong-video-nguoi-que/QUY-TRINH-SAN-XUAT.txt) — quy trình đầy đủ, mọi tham số
- [`HDSD.md`](xuong-video-nguoi-que/HDSD.md) — hướng dẫn ngắn, 6 lệnh làm một tập

**Chạy một tập, một lệnh:**

```bash
python hang_ngay.py
```

**Hoặc đi từng bước:**

```bash
python xuong.py soan          <ten>   # chấm điểm kịch bản, vẽ thẻ, đếm ảnh cần
python xuong.py hf_danh_sach  <ten>   # ra danh sách prompt ảnh
python xuong.py hf_nhap       <ten> <ten>/hf_urls.json
python xuong.py giong         <ten>   # lồng giọng
python xuong.py render        <ten>   # → output.mp4 + thumbnail
```

### Cấu hình

Đổi tên `config.example.json` thành `config.json` rồi điền khoá của bạn:

```bash
cp xuong-video-nguoi-que/config.example.json xuong-video-nguoi-que/config.json
```

`config.json` đã nằm trong `.gitignore` — đừng commit nó lên.

---

## Phần 2 — Skill tự edit video

`skills/edit-viet-social` biến một video quay thô (nói trước camera hoặc quay màn
hình) thành bản dựng có: cắt khoảng lặng, phụ đề tô từ khoá, chữ nhấn 2 dòng,
bảng đồ hoạ, chip giao diện, widget số liệu, chỉnh màu.

```bash
python skills/edit-viet-social/tools/autoedit.py "video-tho.mp4" \
  --out "thu-muc-lam-viec" --model medium \
  --prompt "tên riêng, thuật ngữ trong video"
```

Chạy xong ra `plan.json` — **sửa file đó rồi render lại** là cách để bản dựng ra
giống ý mình. Chi tiết trong [`SKILL.md`](skills/edit-viet-social/SKILL.md) và
[`STYLE.md`](skills/edit-viet-social/references/STYLE.md).

---

## Cài skill vào Claude Code

Chép hai thư mục trong `skills/` vào thư mục skill của bạn:

```bash
cp -r skills/video-sinhton      ~/.claude/skills/
cp -r skills/edit-viet-social   ~/.claude/skills/
```

Rồi gọi `/video-sinhton` hoặc `/edit-viet-social` trong Claude Code.

---

## Những thứ KHÔNG có trong repo này

Đã lược bỏ có chủ đích:

- `config.json` thật (chứa token Telegram, khoá OmniVoice) — dùng bản `.example`
- Hồ sơ trình duyệt đã đăng nhập (`muse_profile/`, `flow_profile/`, `yt_profile/`)
- File giọng mẫu dùng để clone (`voice_ref/`)
- Nhạc nền, bộ nhận diện kênh, và toàn bộ tập đã sản xuất

Bạn tự tạo lại những thứ này cho kênh của mình.

---

## Lưu ý khi dùng lại

- Đổi tên kênh, ID kênh và bot Telegram trong `config.json` sang của bạn —
  trong repo đã thay bằng chỗ trống.
- `kho_de_tai.md` là kho đề tài mẫu. Nếu nhiều người cùng chạy y nguyên thì các
  kênh sẽ ra video trùng nhau — nên viết kho đề tài riêng.
- Font trong `fonts/` là Google Fonts (giấy phép OFL), dùng thương mại được.

## Giấy phép

MIT — xem [LICENSE](LICENSE). Dùng tự do, không bảo hành.
