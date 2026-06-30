# ainovel-cli — Hướng dẫn sử dụng tiếng Việt cho người mới bắt đầu

`ainovel-cli` là công cụ dòng lệnh/TUI dùng AI để tự động sáng tác tiểu thuyết dài. Bạn đưa ra ý tưởng ban đầu, công cụ sẽ điều phối nhiều “tác nhân” AI như **Coordinator**, **Architect**, **Writer** và **Editor** để lập dàn ý, viết chương, kiểm tra chất lượng, tóm tắt arc/volume và tiếp tục viết cho tới khi hoàn thành.

Tài liệu này tập trung vào cách **cài đặt, cấu hình và sử dụng** cho người mới. Nếu bạn chỉ muốn chạy thử nhanh, hãy đọc phần “Bắt đầu nhanh”.

---

## 1. Công cụ này làm được gì?

`ainovel-cli` phù hợp khi bạn muốn:

- Biến một ý tưởng ngắn thành tiểu thuyết dài nhiều chương.
- Tự động lập premise, dàn ý, nhân vật, thế giới quan và nhịp truyện.
- Viết từng chương với khả năng ghi nhớ bối cảnh, trạng thái nhân vật, phục bút và chương liên quan.
- Tự đánh giá chất lượng chương qua Editor AI.
- Dừng rồi chạy tiếp nhờ checkpoint.
- Chạy giao diện TUI để theo dõi tiến độ hoặc chạy headless trên server/NAS.
- Xuất truyện ra TXT hoặc EPUB.
- Nhập truyện có sẵn để phân tích và viết tiếp.
- Tạo/nhập hồ sơ mô phỏng văn phong.

Nói ngắn gọn: đây là “xưởng viết tiểu thuyết AI” chạy trong terminal.

---

## 2. Kiến thức cần biết trước

### 2.1. CLI và TUI là gì?

- **CLI**: chương trình chạy bằng lệnh trong Terminal/PowerShell.
- **TUI**: giao diện dạng text trong terminal. Bạn vẫn dùng bàn phím để nhập lệnh, nhưng có khung trạng thái, danh sách sự kiện, tiến độ, model, token/cost...

### 2.2. Bạn cần API key AI

Công cụ cần một provider LLM như OpenRouter, Anthropic, Gemini, OpenAI, DeepSeek, Qwen, GLM, Grok, Mimo, Ollama, Bedrock hoặc proxy tương thích.

Với người mới, cách dễ nhất thường là dùng **OpenRouter** vì hỗ trợ nhiều model qua một API key.

Bạn cần chuẩn bị:

- API key.
- Base URL nếu provider/proxy yêu cầu.
- Tên model muốn dùng, ví dụ `google/gemini-2.5-flash` hoặc model khác provider của bạn hỗ trợ.

> Không commit/publish API key lên GitHub. File cấu hình có key nên để trong máy cá nhân.

---

## 3. Cài đặt

### 3.1. macOS / Linux — cài một lệnh

```bash
curl -fsSL https://raw.githubusercontent.com/voocel/ainovel-cli/main/scripts/install.sh | sh
```

Cài phiên bản cụ thể:

```bash
curl -fsSL https://raw.githubusercontent.com/voocel/ainovel-cli/main/scripts/install.sh | sh -s -- v1.2.3
```

Cài vào thư mục tùy chọn:

```bash
AINOVEL_INSTALL_DIR="$HOME/.local/bin" \
  curl -fsSL https://raw.githubusercontent.com/voocel/ainovel-cli/main/scripts/install.sh | sh
```

Sau khi cài, kiểm tra:

```bash
ainovel-cli --version
```

Nếu terminal báo không tìm thấy `ainovel-cli`, hãy kiểm tra thư mục cài đặt có nằm trong biến môi trường `PATH` chưa.

### 3.2. Windows — tải thủ công từ Releases

Trên Windows, script cài nhanh không hỗ trợ trực tiếp. Làm như sau:

1. Mở trang Releases: <https://github.com/voocel/ainovel-cli/releases/latest>
2. Tải gói phù hợp với Windows.
3. Giải nén file tải về.
4. Đặt `ainovel-cli.exe` vào một thư mục cố định, ví dụ:
   - `C:\Tools\ainovel-cli\ainovel-cli.exe`
5. Thêm thư mục đó vào `PATH` nếu muốn gọi lệnh từ mọi nơi.
6. Mở PowerShell mới và chạy:

```powershell
ainovel-cli --version
```

Nếu chưa thêm `PATH`, chạy bằng đường dẫn đầy đủ:

```powershell
C:\Tools\ainovel-cli\ainovel-cli.exe --version
```

### 3.3. Cài bằng Go

Nếu đã có Go:

```bash
go install github.com/voocel/ainovel-cli/cmd/ainovel-cli@latest
```

Sau đó đảm bảo thư mục `$GOPATH/bin` hoặc `$HOME/go/bin` nằm trong `PATH`.

### 3.4. Chạy bằng Docker

Docker phù hợp nếu bạn muốn chạy trên server, NAS hoặc môi trường tách biệt.

Tạo thư mục lưu cấu hình và dữ liệu:

```bash
mkdir -p config workspace
```

Chạy TUI:

```bash
docker run --rm -it \
  -v "$PWD/config:/root/.ainovel" \
  -v "$PWD/workspace:/workspace" \
  ghcr.io/voocel/ainovel-cli:latest
```

Chạy headless:

```bash
docker run --rm \
  -v "$PWD/config:/root/.ainovel" \
  -v "$PWD/workspace:/workspace" \
  ghcr.io/voocel/ainovel-cli:latest \
  --headless --prompt "Viết một tiểu thuyết huyền huyễn phương Đông, nhân vật chính khởi đầu từ một thành nhỏ vùng biên."
```

Dùng Docker Compose:

```bash
docker compose run --rm ainovel
```

Hoặc headless:

```bash
docker compose run --rm ainovel --headless --prompt "Viết một truyện đô thị dị năng 80 chương."
```

---

## 4. Bắt đầu nhanh

### Bước 1 — Chạy lần đầu

```bash
ainovel-cli
```

Nếu chưa có cấu hình, chương trình sẽ mở quy trình thiết lập lần đầu:

1. Chọn provider.
2. Nhập API key.
3. Nhập Base URL nếu cần.
4. Nhập model.
5. Lưu cấu hình.

Sau khi xong, chương trình vào TUI.

### Bước 2 — Nhập yêu cầu viết truyện

Trong ô nhập của TUI, gõ prompt mô tả truyện bạn muốn viết, ví dụ:

```text
Viết một tiểu thuyết tiên hiệp 120 chương. Nhân vật chính là một dược đồng bị mất linh căn, vô tình phát hiện một lò luyện đan cổ có ý thức. Phong cách chậm rãi, nhiều mưu kế, ít hệ thống.
```

Nhấn Enter để bắt đầu.

> Lưu ý quan trọng: phiên bản hiện tại **không còn hỗ trợ truyền prompt trực tiếp như đối số thường** kiểu `ainovel-cli "viết truyện..."`. Nếu muốn truyền prompt từ command line, phải dùng `--headless --prompt`.

### Bước 3 — Theo dõi tiến độ

Trong TUI, bạn sẽ thấy:

- Trạng thái hiện tại của truyện.
- Agent đang làm việc.
- Chương đang viết/đang review.
- Token/cost nếu provider hỗ trợ.
- Nhật ký sự kiện.
- Thư mục output.

### Bước 4 — Can thiệp khi cần

Bạn có thể nhập góp ý trực tiếp khi truyện đang chạy, ví dụ:

```text
Từ chương sau, tăng yếu tố điều tra phá án, giảm cảnh tu luyện lặp lại.
```

Hệ thống sẽ đánh giá ảnh hưởng và xử lý phần cần chỉnh/sửa/viết tiếp.

---

## 5. Cấu hình

### 5.1. File cấu hình nằm ở đâu?

Mặc định:

- macOS/Linux: `~/.ainovel/config.json`
- Windows: thường là `%USERPROFILE%\.ainovel\config.json`

Ngoài ra có thể dùng cấu hình theo dự án:

```text
./.ainovel/config.json
```

Thứ tự ưu tiên theo code hiện tại:

1. Cấu hình global trong `~/.ainovel/config.json`.
2. Cấu hình project trong `./.ainovel/config.json`.
3. Đường dẫn chỉ định bằng `--config`.

Bạn có thể chạy bằng cấu hình riêng:

```bash
ainovel-cli --config ./my-config.json
```

### 5.2. Ví dụ cấu hình tối giản với OpenRouter

```json
{
  "provider": "openrouter",
  "model": "google/gemini-2.5-flash",
  "reasoning_effort": "medium",
  "providers": {
    "openrouter": {
      "api_key": "sk-or-v1-xxx",
      "base_url": "https://openrouter.ai/api/v1",
      "models": [
        "google/gemini-2.5-flash",
        "anthropic/claude-sonnet-4"
      ]
    }
  },
  "style": "default"
}
```

Trong đó:

- `provider`: tên cấu hình provider đang dùng. Đây là “con trỏ” trỏ tới một key trong `providers`, không nhất thiết là tên giao thức.
- `model`: model mặc định.
- `reasoning_effort`: mức suy luận mặc định, có thể là `off`, `low`, `medium`, `high`, `xhigh`, `max`.
- `providers`: kho credential/provider.
- `style`: phong cách viết mặc định. Repo hiện có các style như `default`, `suspense`, `fantasy`, `romance`.

### 5.3. Cấu hình nhiều provider

```json
{
  "provider": "openrouter",
  "model": "google/gemini-2.5-flash",
  "providers": {
    "openrouter": {
      "api_key": "sk-or-v1-xxx",
      "base_url": "https://openrouter.ai/api/v1",
      "models": ["google/gemini-2.5-flash"]
    },
    "anthropic": {
      "api_key": "sk-ant-xxx",
      "models": ["claude-sonnet-4", "claude-opus-4"]
    },
    "ollama": {
      "base_url": "http://localhost:11434/v1",
      "models": ["qwen3:14b"]
    }
  },
  "style": "default"
}
```

### 5.4. Cấu hình model theo vai trò

Bạn có thể dùng model khác nhau cho từng agent:

```json
{
  "provider": "openrouter",
  "model": "google/gemini-2.5-flash",
  "reasoning_effort": "medium",
  "providers": {
    "openrouter": {
      "api_key": "sk-or-v1-xxx",
      "base_url": "https://openrouter.ai/api/v1",
      "models": ["google/gemini-2.5-flash", "google/gemini-2.5-pro"]
    },
    "anthropic": {
      "api_key": "sk-ant-xxx",
      "models": ["claude-sonnet-4"]
    }
  },
  "roles": {
    "writer": {
      "provider": "anthropic",
      "model": "claude-sonnet-4",
      "reasoning_effort": "high"
    },
    "architect": {
      "provider": "openrouter",
      "model": "google/gemini-2.5-pro"
    }
  },
  "style": "fantasy"
}
```

Các role thường gặp:

- `coordinator`: điều phối tổng thể.
- `architect`: dựng tiền đề, dàn ý, nhân vật, thế giới quan.
- `writer`: viết chương.
- `editor`: đánh giá/chỉnh chất lượng.

### 5.5. Cấu hình budget

Nếu sợ chạy quá tốn tiền, đặt ngân sách cho một cuốn:

```json
{
  "budget": {
    "book_usd": 50,
    "warn_ratio": 0.8,
    "hard_stop": false
  }
}
```

Ý nghĩa:

- `book_usd`: ngân sách tối đa cho một cuốn. `0` hoặc bỏ trống nghĩa là không bật giới hạn.
- `warn_ratio`: cảnh báo khi chạm tỷ lệ ngân sách, ví dụ `0.8` là 80%.
- `hard_stop`: nếu `true`, dừng ngay khi vượt ngân sách; nếu `false`, dừng mềm sau khi tác vụ agent hiện tại kết thúc.

### 5.6. Cấu hình thông báo

Có thể cấu hình command để báo khi kết thúc/chạm ngân sách/lặp bất thường:

```json
{
  "notify": {
    "enabled": true,
    "command": "curl -s -G --data-urlencode \"title=$NOTIFY_TITLE\" --data-urlencode \"message=$NOTIFY_BODY\" ntfy.sh/my-topic/publish",
    "events": ["run_end", "repeat", "budget"]
  }
}
```

Các biến môi trường được truyền vào command:

- `NOTIFY_TITLE`
- `NOTIFY_BODY`
- `NOTIFY_KIND`
- `NOTIFY_LEVEL`

---

## 6. Các cách chạy

### 6.1. Chạy TUI bình thường

```bash
ainovel-cli
```

Dùng khi bạn muốn quan sát tiến độ, đổi model, nhập lệnh slash và can thiệp trong lúc viết.

### 6.2. Chạy headless bằng prompt

```bash
ainovel-cli --headless --prompt "Viết một tiểu thuyết đô thị 60 chương về một bác sĩ Đông y xuyên về năm 1998."
```

Lưu ý:

- Headless không hỗ trợ thiết lập lần đầu. Bạn phải chạy TUI ít nhất một lần để tạo config trước.
- `--prompt` chỉ dùng được cùng `--headless`.

### 6.3. Chạy headless bằng file prompt

Tạo file `prompt.txt`:

```text
Viết một truyện huyền nghi 80 chương.
Bối cảnh: thị trấn ven biển, mỗi đêm có một ngọn hải đăng phát sáng dù đã bỏ hoang.
Phong cách: chậm, u ám, nhiều chi tiết điều tra.
```

Chạy:

```bash
ainovel-cli --headless --prompt-file prompt.txt
```

Đọc prompt từ stdin:

```bash
cat prompt.txt | ainovel-cli --headless --prompt-file -
```

### 6.4. Kiểm tra phiên bản

```bash
ainovel-cli --version
```

Hoặc:

```bash
ainovel-cli version
```

### 6.5. Cập nhật

```bash
ainovel-cli update
```

Cập nhật lên phiên bản cụ thể:

```bash
ainovel-cli update v1.2.3
```

---

## 7. Lệnh trong TUI

Gõ các lệnh sau vào ô nhập của TUI.

### 7.1. `/help`

Hiển thị danh sách lệnh và phím tắt.

```text
/help
```

### 7.2. `/model [role]`

Mở panel đổi model.

```text
/model
/model writer
/model architect
/model editor
/model coordinator
```

Dùng khi:

- Muốn đổi model mặc định.
- Muốn Writer dùng model mạnh hơn Architect.
- Muốn giảm chi phí bằng model rẻ hơn cho role phụ.

### 7.3. `/diag`

Chẩn đoán “sức khỏe” quá trình sáng tác.

```text
/diag
```

Công cụ sẽ tạo báo cáo trong thư mục output, thường có file dạng:

```text
meta/diag-export.md
```

Dùng khi:

- Truyện bị lặp.
- Chất lượng giảm.
- Agent có dấu hiệu đi sai hướng.
- Cần gửi thông tin debug khi báo lỗi.

### 7.4. `/import <path> [from=N]`

Nhập một truyện/tài liệu bên ngoài để hệ thống phân tích và viết tiếp.

```text
/import ~/novel.txt
/import ~/novel.txt from=50
```

Ý nghĩa:

- Không có `from=N`: phân tích từ đầu.
- Có `from=50`: coi từ chương 50 là điểm bắt đầu để viết tiếp.

Dùng khi bạn đã có bản thảo cũ và muốn AI tiếp tục cùng thế giới/nhân vật/phong cách.

### 7.5. `/cocreate` hoặc `/plan`

Tạm dừng để cùng hoạch định giai đoạn tiếp theo.

```text
/cocreate
/plan
```

Dùng khi đang viết và bạn muốn định hướng lại:

- Đẩy nhanh một tuyến truyện.
- Thêm phản diện mới.
- Chuyển trọng tâm từ tu luyện sang điều tra.
- Quyết định arc tiếp theo cùng AI.

### 7.6. `/simulate`

Đọc dữ liệu trong thư mục `./simulate` để tạo hoặc cập nhật hồ sơ mô phỏng văn phong.

```text
/simulate
```

Cách dùng gợi ý:

1. Tạo thư mục `simulate` trong thư mục chạy chương trình.
2. Bỏ các file văn bản mẫu vào đó.
3. Chạy `/simulate`.
4. Dùng hồ sơ sinh ra để định hướng văn phong cho truyện.

### 7.7. `/importsim <profile.json>`

Nhập hồ sơ mô phỏng văn phong đã có.

```text
/importsim ./profile.json
```

Dùng khi bạn đã tạo profile từ lần trước hoặc từ máy khác.

### 7.8. `/export [path] [from=N] [to=M] [--overwrite]`

Xuất các chương đã hoàn thành ra TXT hoặc EPUB.

```text
/export
/export ~/truyen.txt
/export ~/truyen.epub
/export from=10 to=30 --overwrite
/export from=10 ~/truyen.epub --overwrite
```

Ghi chú:

- Không truyền path: xuất TXT mặc định vào thư mục truyện.
- Đuôi `.txt`: xuất TXT.
- Đuôi `.epub`: xuất EPUB.
- `from=N`, `to=M`: chỉ xuất một khoảng chương.
- `--overwrite`: cho phép ghi đè file đã tồn tại.

---

## 8. Dữ liệu được lưu ở đâu?

Mặc định, output nằm trong:

```text
output/novel
```

Bên trong có thể có:

- Tiến độ truyện.
- Checkpoint.
- Dàn ý.
- Bản nháp/chương đã commit.
- Tóm tắt chương/arc/volume.
- Báo cáo chẩn đoán.
- File export TXT/EPUB.

Khi TUI đang chạy, phần thông tin bên phải/khung trạng thái sẽ hiển thị thư mục output hiện tại.

---

## 9. Quy trình viết truyện khuyến nghị cho người mới

### 9.1. Chuẩn bị prompt tốt

Một prompt tốt nên có:

- Thể loại: tiên hiệp, đô thị, huyền nghi, romance, fantasy...
- Độ dài dự kiến: 30/60/120/300 chương.
- Nhân vật chính: xuất thân, mục tiêu, điểm yếu.
- Mâu thuẫn chính.
- Tông giọng: hài, u ám, chậm rãi, nhiều mưu kế, ít combat...
- Điều không muốn: không hệ thống, không harem, không lạm dụng plot armor...

Ví dụ:

```text
Viết một tiểu thuyết huyền nghi đô thị 80 chương.
Nhân vật chính là bác sĩ pháp y 32 tuổi, từng mắc sai lầm trong một vụ án cũ.
Bối cảnh là một thành phố ven biển luôn có sương mù vào ban đêm.
Tông truyện u ám, nhịp chậm, tập trung điều tra và tâm lý nhân vật.
Không dùng yếu tố hệ thống, không lạm dụng siêu nhiên; mọi chi tiết kỳ lạ cuối cùng phải có lời giải hợp lý hoặc bán hợp lý.
```

### 9.2. Chạy thử ngắn trước

Nếu mới dùng, đừng bắt đầu ngay bằng truyện 500 chương. Hãy thử 20–30 chương để kiểm tra:

- Provider/model có chạy ổn không.
- Chi phí/token ra sao.
- Văn phong có hợp không.
- Export có đúng nhu cầu không.

### 9.3. Dùng `/model` để cân bằng chất lượng và chi phí

Gợi ý:

- Writer: model mạnh, văn tốt.
- Architect: model giỏi lập kế hoạch.
- Editor: model ổn định, biết soi lỗi.
- Coordinator: model đủ dài context và tuân thủ tốt.

Nếu chi phí cao, giảm model ở Architect/Editor trước, giữ Writer tốt hơn.

### 9.4. Can thiệp sớm khi thấy lệch

Đừng chờ 50 chương mới sửa hướng. Nếu thấy dấu hiệu lệch ở chương 3–5, nhập chỉ dẫn ngay:

```text
Giữ nhịp chậm hơn. Đừng cho nhân vật chính thắng quá dễ. Mỗi chiến thắng phải đổi bằng tổn thất cụ thể.
```

---

## 10. Ví dụ sử dụng thực tế

### 10.1. Viết truyện tiên hiệp bằng TUI

```bash
ainovel-cli
```

Trong TUI nhập:

```text
Viết một truyện tiên hiệp 100 chương. Nhân vật chính là dược đồng ở tông môn nhỏ, tư chất kém nhưng có khả năng nghe được ký ức còn sót trong linh dược. Truyện thiên về luyện đan, mưu kế môn phái, ít đánh nhau trực diện. Tông giọng cổ phong, chậm rãi, nhiều phục bút.
```

### 10.2. Chạy server headless

```bash
ainovel-cli --headless --prompt "Viết một truyện khoa huyễn 60 chương về một kỹ sư bảo trì trạm không gian phát hiện nhật ký của AI đã chết."
```

### 10.3. Xuất EPUB sau khi có chương

Trong TUI:

```text
/export ./my-novel.epub --overwrite
```

### 10.4. Viết tiếp từ bản thảo cũ

Trong TUI:

```text
/import ./ban-thao-cu.txt from=42
```

Sau khi hệ thống phân tích xong, đưa yêu cầu viết tiếp:

```text
Viết tiếp từ chương 42. Giữ văn phong cũ, nhưng tăng tuyến điều tra và giảm thoại giải thích trực tiếp.
```

---

## 11. Xử lý lỗi thường gặp

### 11.1. `headless 模式不支持首次引导`

Nghĩa là bạn chạy headless khi chưa có config.

Cách sửa:

```bash
ainovel-cli
```

Hoàn tất thiết lập trong TUI trước, sau đó mới chạy:

```bash
ainovel-cli --headless --prompt "..."
```

### 11.2. Báo lỗi provider chưa cấu hình credential

Kiểm tra `provider` ở đầu file config có trỏ đúng key trong `providers` không.

Ví dụ sai:

```json
{
  "provider": "openrouter",
  "providers": {
    "my-openrouter": {
      "api_key": "..."
    }
  }
}
```

Sửa một trong hai cách:

```json
{
  "provider": "my-openrouter"
}
```

Hoặc đổi key trong `providers` thành `openrouter`.

### 11.3. `--prompt/--prompt-file` không chạy trong TUI

`--prompt` và `--prompt-file` chỉ dùng với `--headless`.

Đúng:

```bash
ainovel-cli --headless --prompt "Viết một truyện..."
```

Sai:

```bash
ainovel-cli --prompt "Viết một truyện..."
```

### 11.4. Truyền prompt trực tiếp sau lệnh bị lỗi

Phiên bản hiện tại không hỗ trợ:

```bash
ainovel-cli "Viết một truyện..."
```

Hãy dùng một trong hai cách:

```bash
ainovel-cli
```

Rồi nhập prompt trong TUI, hoặc:

```bash
ainovel-cli --headless --prompt "Viết một truyện..."
```

### 11.5. Windows mở lên rồi tắt ngay

Nếu chạy bằng double-click và có lỗi, console có thể đóng nhanh. Hãy mở PowerShell trước, rồi chạy:

```powershell
ainovel-cli
```

Nếu vẫn lỗi, kiểm tra file:

```text
%USERPROFILE%\.ainovel\last-error.log
```

### 11.6. Không xuất được file vì đã tồn tại

Thêm `--overwrite`:

```text
/export ./truyen.epub --overwrite
```

### 11.7. Truyện bị lặp hoặc chất lượng giảm

Thử:

```text
/diag
```

Sau đó can thiệp bằng chỉ dẫn rõ hơn:

```text
Các chương sau phải giảm lặp lại cảnh tu luyện. Mỗi chương cần có một biến cố mới làm thay đổi quan hệ nhân vật hoặc thông tin điều tra.
```

### 11.8. Chi phí tăng quá nhanh

- Đặt `budget.book_usd`.
- Dùng model rẻ hơn cho Architect/Editor.
- Chạy truyện ngắn để test trước.
- Giảm độ dài truyện.
- Tránh yêu cầu quá rộng kiểu “500 chương, mỗi chương thật dài” khi chưa đo chi phí.

---

## 12. Mẹo viết prompt hay

### Prompt yếu

```text
Viết truyện tu tiên hay.
```

Vấn đề: quá chung, AI phải tự đoán gần như toàn bộ.

### Prompt tốt hơn

```text
Viết truyện tu tiên 120 chương, phong cách chậm rãi, nhiều mưu kế môn phái.
Nhân vật chính là một dược đồng tư chất thấp, bị xem thường nhưng cực kỳ giỏi quan sát độc tính linh thảo.
Xung đột chính: cậu phát hiện tông môn đang dùng đệ tử ngoại môn làm vật thử cho một loại đan dược cấm.
Không harem, không hệ thống, không để nhân vật chính tăng cấp quá nhanh.
Mỗi arc cần có bí mật được hé lộ và một cái giá phải trả.
```

### Công thức prompt gợi ý

```text
Viết một tiểu thuyết [thể loại] khoảng [số chương] chương.
Bối cảnh: [thế giới/thời đại/địa điểm].
Nhân vật chính: [xuất thân + năng lực + điểm yếu + mục tiêu].
Mâu thuẫn chính: [vấn đề lớn].
Tông giọng: [chậm/nhanh/u ám/hài/ấm áp/căng thẳng].
Điểm nhấn: [mưu kế/tình cảm/điều tra/chiến đấu/sinh tồn].
Tránh: [những thứ không muốn].
Yêu cầu đặc biệt: [quy tắc thế giới, nhịp arc, motif, giới hạn].
```

---

## 13. Lưu ý bảo mật và chi phí

- Không chia sẻ `~/.ainovel/config.json` nếu trong đó có API key.
- Không đưa API key vào issue công khai.
- Chạy truyện dài có thể tốn nhiều token và tiền.
- Hãy bật budget nếu chạy qua đêm hoặc chạy trên server.
- Khi dùng Docker, thư mục `config` được mount vào `/root/.ainovel`; hãy bảo vệ thư mục này.
- Khi báo lỗi, dùng `/diag` và kiểm tra file export đã được redact thông tin nhạy cảm trước khi gửi.

---

## 14. Cheatsheet lệnh nhanh

```bash
# Chạy TUI
ainovel-cli

# Xem phiên bản
ainovel-cli --version
ainovel-cli version

# Cập nhật
ainovel-cli update
ainovel-cli update v1.2.3

# Chạy với config riêng
ainovel-cli --config ./config.json

# Headless với prompt trực tiếp
ainovel-cli --headless --prompt "Viết một truyện..."

# Headless với file prompt
ainovel-cli --headless --prompt-file ./prompt.txt
```

Lệnh trong TUI:

```text
/help
/model
/model writer
/diag
/import ./novel.txt from=50
/cocreate
/plan
/simulate
/importsim ./profile.json
/export
/export ./novel.txt --overwrite
/export ./novel.epub --overwrite
/export from=10 to=30 --overwrite
```

---

## 15. Lộ trình học cho người mới

1. Cài `ainovel-cli`.
2. Chạy `ainovel-cli --version` để kiểm tra.
3. Chạy `ainovel-cli` và cấu hình provider.
4. Viết thử truyện 10–30 chương.
5. Dùng `/model` để đổi model nếu văn chưa ổn.
6. Dùng `/diag` nếu truyện lặp hoặc lệch.
7. Dùng `/export` để lấy TXT/EPUB.
8. Khi đã quen, hãy thử headless/Docker để chạy tác vụ dài.

---

## 16. Nguồn tham khảo trong repo

- `README.md`: tài liệu gốc tiếng Trung.
- `config.example.jsonc`: ví dụ cấu hình đầy đủ.
- `cmd/ainovel-cli/main.go`: flag CLI chính.
- `internal/entry/tui/commands.go`: danh sách lệnh slash trong TUI.
- `docker-compose.yml`: cấu hình Docker Compose mẫu.
- `scripts/install.sh`: script cài đặt macOS/Linux.

---

## 17. Tóm tắt cực ngắn

Nếu bạn chỉ nhớ 5 dòng:

```bash
ainovel-cli --version
ainovel-cli
# cấu hình API key trong lần chạy đầu
# nhập ý tưởng truyện trong TUI
# xuất truyện bằng /export ./truyen.epub --overwrite
```

Vậy là đủ để bắt đầu.
