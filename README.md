# bamboo — reference only

> **Kho này chỉ có một mục đích duy nhất: đối chiếu logic với engine C++ mới.**
>
> Toàn bộ code ở đây là bản **Go cũ** của engine gõ tiếng Việt, giữ lại làm mốc so sánh cho bản port
> C++ trong `lotus/` của [LotusInputMethod/fcitx5-lotus](https://github.com/LotusInputMethod/fcitx5-lotus).
> **Không khuyến khích sử dụng** cho bất kỳ mục đích nào khác: không dùng làm thư viện, không nhúng vào
> sản phẩm, không nhận fix/tính năng mới, không có hỗ trợ. Engine đang chạy thật là bản C++; repo này
> chỉ để tra cứu hành vi gốc khi cần đối chiếu.

## Cấu trúc

| Đường dẫn | Nội dung |
|---|---|
| `bamboo-core/` | engine gõ tiếng Việt gốc (Go), module riêng `github.com/LotusInputMethod/bamboo-core`, MIT |
| `*.go` ở root | glue fcitx5 (cgo) + `golden_dump_test.go` (dumper corpus), GPL-3.0-or-later |

## Chạy test

```sh
cd bamboo-core && go test ./...   # engine thuần Go, không cần fcitx
go test ./...                     # glue + dumper: cần Go >= 1.18, cgo và header dev của fcitx5
```

Header dev cần cho phần glue: `libfcitx5core-dev`, `libfcitx5config-dev`, `libfcitx5utils-dev`.

## Sinh lại golden corpus (dùng cho bản C++)

`golden_dump_test.go` lái chính engine Go này và ghi ra 3 file TSV (`charset.tsv`, `engine_core.tsv`,
`key_processor.tsv`) là **nguồn sự thật** để bản C++ đối chiếu từng byte:

```sh
# mặc định ghi vào ./golden, đổi bằng LOTUS_GOLDEN_OUT_DIR
LOTUS_DUMP_GOLDEN=1 go test -run TestDumpGoldenCorpus -count=1
```

- Cấu hình được: `LOTUS_GOLDEN_OUT_DIR` (thư mục ra), `LOTUS_GOLDEN_SEED` (mặc định `20261007`),
  `LOTUS_GOLDEN_RANDOM_COUNT` (mặc định `2000`), `LOTUS_GOLDEN_MAX_LENGTH` (mặc định `12`).
- Không đặt `LOTUS_DUMP_GOLDEN=1` thì dumper tự skip, `go test ./...` không ghi file.
- Phía C++ đọc corpus qua `LOTUS_GOLDEN_DIR`, ví dụ:
  `LOTUS_GOLDEN_DIR=/đường/dẫn/golden ./build/test/lotus_engine_test` (và `./build/test/lotus_key_processor`).
- Quy tắc bất di bất dịch: **corpus là nguồn sự thật** — lệch thì sửa code C++, không sửa corpus.

## Giấy phép / ghi công

- `bamboo-core/` — MIT, © 2018 Luong Thanh Lam <ltlam93@gmail.com> (giữ nguyên ở `bamboo-core/LICENSE`).
- Glue fcitx5 + dumper — GPL-3.0-or-later, © 2022 CSSlayer <wengxt@gmail.com>, © 2026 Nguyen Hoang Ky <nhktmdzhg@gmail.com>.
- `LICENSE` ở root là GPL-3.0-or-later cho phần glue; giấy phép MIT của bamboo-core nằm trong
  `bamboo-core/LICENSE`.
- Bản C++ (`lotus/` trong `fcitx5-lotus`) là GPL-3.0-or-later và chỉ **ghi công** MIT cho bamboo-core.
