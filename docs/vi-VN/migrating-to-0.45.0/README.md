# Tích hợp sang v0.45.0

Starship v0.45.0 là một bản phát hành chứa nhiều thay đổi trong việc chuẩn bị cho bản thay đổi lớn v1.0.0. Chúng tôi tạo một vài thay đổi xung quanh cách cấu hình được thực hiện trên dáu nhắc lệnh, cho phép tùy biến theo góc độ tốt hơn.

Hướng dẫn này nhằm hướng dẫn bạn vượt qua những sự thay đổi.

## `prompt_order` được thay thế boiwr một root-level `format`

Từ trước đế v0.45.0, `prompt_order` sẽ chấp nhận một mảng các tên mô đun theo thứ tự mà chúng nên được render bởi Starship.

Starship v0.45.0 thay vì chấp nhận một giá trị `format`, nó cho phép tùy biến dấu nhắc lệnh bên ngoài chính các mô đun đó.

**Ví dụcủa cấu hình pre-v0.45.0**

```toml
prompt_order = [
  "username",
  "hostname",
  "directory",
  "git_branch",
  "git_commit",
  "git_state",
  "git_status",
  "cmd_duration",
  "custom",
  "line_break",
  "jobs",
  "battery",
  "time",
  "character",
]
```

**Ví dụcủa cấu hình v0.45.0**

```toml
format = """\
  $username\
  $hostname\
  $directory\
  $git_branch\
  $git_commit\
  $git_state\
  $git_status\
  $cmd_duration\
  $custom\
  $line_break\
  $jobs\
  $battery\
  $time\
  $character\
  """
```

## Mô đun `prefix` và`suffix` thay bằng `format`

Từ trước tới v0.45.0, một vài mô đun sẽ chấp nhận `prefix` và/hoặc `suffix` theo thứ tự để stylize các mà các mô đun được render.

Starship v0.45.0 thay vì chấp nhận một giá trị `format`, nó cho phép tùy biến dấu nhắc lệnh bên ngoài chính các mô đun đó. Thay vì định nghĩa một tiền tố và hậu tố cho các giá trị context-based, các giá trị bây giờ có thể được thay thế với một format string, cái đại diện cho đầu ra của module.

**Ví dụ của cấu hình pre-v0.45.0**

```toml
[cmd_duration]
prefix = "took "
```

**Ví dụ của cấu hình v0.45.0**

```toml
[cmd_duration]
# $duration – Thời gian câu lệnh dùng để thực thi (e.g. "15s")
# $style    – Style mặc định của mô đun (e.g. "bold yellow")
format = "took [$duration]($style) "
```

### Các mô đun ảnh hưởng

#### Character

| Thuộc tính bị gỡ bỏ     | Thay thế bằng    |
| ----------------------- | ---------------- |
| `symbol`                | `success_symbol` |
| `use_symbol_for_status` | `error_symbol`   |
| `style_success`         | `success_symbol` |
| `style_failure`         | `error_symbol`   |

**Các thay đổi về cấu hình mặc định**

```diff
[character]
-- symbol = "❯"
-- error_symbol = "✖"
-- use_symbol_for_status = true
-- vicmd_symbol = "❮"
++ success_symbol = "[❯](bold green)"
++ error_symbol = "[❯](bold red)"
++ vicmd_symbol = "[❮](bold green)"
```

Trước đây, thuộc tính `use_symbol_for_status` được sử dụng để cấu hình dấu nhắc lệnh hiển thị `error_symbol` khi câu lệnh cuối cùng trả về kết quả có status code khác 0.

Với bản hát hành v0.45.0, chúng ta bây giờ luôn sử dụng `error_symbol` sau các status khác 0, thống nhất các thuộc tính `use_symbol_for_status` và `error_symbol`.

Cấu hình dâu nhắc lệnh để sử dụng cấu hình `use_symbol_for_status = true`, thêm đoạn dưới vào tệp cấu hình của bạn:

```toml
[character]
error_symbol = "[✖](bold red)"
```

*Lưu ý:* Phần tử `character` tự động thêm vào một khoảng trắng phía sau, so unlike the other `format` strings, we specifically do not add one in the above examples.

#### Command Duration

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[cmd_duration]
-- prefix = "took "
++ format = "took [$duration]($style) "
```

#### Đường dẫn

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[directory]
-- prefix = "in "
++ format = "[$path]($style)[$read_only]($read_only_style) "
```

#### Environment Variable

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |
| `suffix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[env_var]
-- prefix = ""
-- suffix = ""
++ format = "with [$env_value]($style) "
```

#### Git Commit

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |
| `suffix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[git_commit]
-- prefix = "("
-- suffix = ")"
++ format = '[\($hash\)]($style) '
```

#### Git Status

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |
| `suffix`            | `format`      |
| `show_sync_count`   | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[git_status]
-- prefix = "["
-- suffix = "]"
-- show_sync_count = false
++ format = '([\[$all_status$ahead_behind\]]($style) )'
```

Previously, the `show_sync_count` property was used to configure the prompt to show the number of commits the branch was ahead or behind the remote branch.

With the release of v0.45.0, this has been replaced with three separate properties, `ahead`, `behind`, and `diverged`.

To configure the prompt to use the older `show_sync_count = true` configuration, set the following to your config file:

```toml
[git_status]
ahead = "⇡${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
behind = "⇣${count}"
```

#### Hostname

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |
| `suffix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[hostname]
-- prefix = ""
-- suffix = ""
++ format = "[$hostname]($style) in "
```

#### Singularity

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `label`             | `format`      |
| `prefix`            | `format`      |
| `suffix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[singularity]
-- prefix = ""
-- suffix = ""
++ format = '[$symbol\[$env\]]($style) '
```

#### Time

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `format`            | `time_format` |

**Các thay đổi về cấu hình mặc định**

```diff
[time]
-- format = "🕙[ %T ]"
++ time_format = "%T"
++ format = "at 🕙[$time]($style) "
```

#### Custom Commands

| Thuộc tính bị gỡ bỏ | Thay thế bằng |
| ------------------- | ------------- |
| `prefix`            | `format`      |
| `suffix`            | `format`      |

**Các thay đổi về cấu hình mặc định**

```diff
[custom.example]
-- prefix = ""
-- suffix = ""
++ format = "[$symbol$output]($style) "
```
