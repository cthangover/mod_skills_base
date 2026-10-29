# Skills Engine — bộ sưu tập thẻ kỹ năng của người chơi

Skills Engine giữ các kỹ năng người chơi đã sưu tầm và hiển thị chúng dưới dạng thẻ trong bảng **Kỹ năng**. Một kỹ năng có thể mở ra khả năng trong một cơ chế khác (cánh "cổng" bị động), trao cho người chơi một thẻ hành động mới cho trận chiến, và cộng số vào **hiệu ứng bộ sưu tập** — tổng của mọi kỹ năng đã sưu tầm.

Mod là một phần của Cthangover, nơi người chơi khởi đầu với đúng một kỹ năng, **Kẻ buôn nô lệ**.

## Bảng Kỹ năng

Mở bằng biểu tượng kỹ năng trên thanh công cụ.

![Bảng Kỹ năng trong Cthangover: thẻ Kẻ buôn nô lệ (Epic, Bị động, +10% khả năng chiêu mộ) trong lưới, mô tả của nó ở khu nghiên cứu thẻ bên phải, và tấm hiệu ứng bộ sưu tập ở góc ghi "+10% khả năng chiêu mộ".](assets/skills-panel.jpg)

- **Thẻ** lọc theo loại — **Bị động**, **Chủ động**, **Dành cho nhân vật** — còn hàng độ hiếm lọc theo độ hiếm, từ **Thường** đến **Huyền thoại**; ô tìm kiếm và danh sách sắp xếp thu hẹp lưới.
- **Màu viền và các hình thoi** cho biết độ hiếm của thẻ.
- **Nhấp vào một thẻ** để ngắm nó trong khu nghiên cứu thẻ: ảnh, huy hiệu độ hiếm và loại, mô tả, các hiệu ứng của nó và, với kỹ năng có tặng kèm, thẻ hành động mà nó cho.
- **Hiệu ứng bộ sưu tập** cộng dồn hiệu ứng của mọi kỹ năng đã sưu tầm: hai kỹ năng +10% và +5% chiêu mộ sẽ hiển thị +15%.

Thẻ Kẻ buôn nô lệ tạm chưa có ảnh: game nêu `characters/skills/slave_master` làm ảnh của nó, nhưng không mod nào kèm tệp đó, nên phần viền còn trống.

## Định nghĩa kỹ năng

Kỹ năng là tệp JSON kind `skill`, đặt ở bất kỳ đâu trong mod. Đây là Kẻ buôn nô lệ của Cthangover — hãy so với bảng phía trên:

```json
{
  "Kind": "skill",
  "Items": [
    {
      "Id": "slave_master",
      "Name": "characters/skills/slave_master_name",
      "Description": "characters/skills/slave_master",
      "Image": "characters/skills/slave_master",
      "Type": 1,
      "Rare": 3,
      "Effects": [
        { "Key": "characters/skills/effects/recruit", "Value": 10, "Percent": true }
      ]
    }
  ]
}
```

- `Name`, `Description` — khóa dịch.
- `Image` — ảnh của thẻ.
- `Type` — `Active` (0), `Passive` (1) hoặc `ForCharacter` (2); tên hay số đều được. Kẻ buôn nô lệ là **bị động**.
- `Rare` — `Common` (0), `Uncommon`, `Rare`, `Epic` (3), `Legendary`. Kẻ buôn nô lệ là **epic**.
- `Effects` — các con số cho hiệu ứng bộ sưu tập: `Key` là khóa dịch của thứ được tăng cường, `Value` là lượng, `Percent` là có hiển thị kèm % hay không. Một khóa phải giữ nguyên đơn vị trong mọi kỹ năng, vì các giá trị được cộng lại.
- `GrantsAction` (tùy chọn) — id của một thẻ hành động (xem Characters Engine). Khi nhận kỹ năng, thẻ này cũng vào kho hành động của bảng Hành động nhân vật.

## Kỹ năng làm gì trong game

Bảng chỉ hiển thị kỹ năng. Kỹ năng đổi gì là do các mod kiểm tra nó quyết định:

- recruiting chỉ đề nghị **biến kẻ địch bị đánh bại thành nô lệ** khi người chơi có `slave_master`. Xác suất thành công là `RecruitmentChance` của chính kẻ địch; +10% trên thẻ chỉ hiện trong bảng, recruiting không cộng nó vào.
- Kỹ năng có `GrantsAction` trao thẻ hành động của nó, người chơi sau đó đặt vào ô của một nhân vật.

## Trao kỹ năng từ kịch bản

```scenario
action character.add_skill skill_id=slave_master
```

Trao một kỹ năng hai lần thì lần sau không có tác dụng, nên cảnh lặp lại không thể nhân đôi kỹ năng hay thẻ hành động của nó. Cthangover trao Kẻ buôn nô lệ khi ván mới bắt đầu.

## Khi có trục trặc

1. **Kỹ năng không xuất hiện.** `skill_id` phải khớp với `Id` của kỹ năng; id lạ được ghi vào log như một cảnh báo.
2. **Thẻ không có ảnh.** Không mod nào cung cấp ảnh mang tên ghi trong `Image`.
3. **Hiệu ứng bộ sưu tập hiện tổng lạ.** Hai kỹ năng dùng cùng khóa hiệu ứng nhưng `Percent` khác nhau.
