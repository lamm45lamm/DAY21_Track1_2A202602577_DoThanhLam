# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- Họ và tên: Đỗ Thanh Lâm
- MSSV / mã học viên: 2A202602577
- Lớp: Track 1
- Ngành đã chọn: Y tế

### 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | AI bỏ sót bệnh nặng (ví dụ nhiễm khuẩn huyết) hoặc đề xuất điều trị sai khiến bệnh nhân được can thiệp muộn hoặc sai; quá nhiều cảnh báo sai làm bác sĩ, điều dưỡng bỏ qua cả cảnh báo đúng. |
| Mức độ high-stakes | Cao. Sai sót có thể dẫn tới tử vong hoặc tổn hại sức khỏe không hồi phục;|
| Dữ liệu nhạy cảm có thể được sử dụng | Hồ sơ bệnh án điện tử (chẩn đoán, tiền sử bệnh, đơn thuốc), kết quả xét nghiệm, hình ảnh y khoa (X-quang, CT), dữ liệu thanh toán/chi phí điều trị và bảo hiểm, thông tin nhân khẩu học (tuổi, giới, chủng tộc/dân tộc), dữ liệu gen. |
| Nhu cầu human review | Cao. Người kiểm tra là bác sĩ/điều dưỡng. Cần review 3 bước: Trước triển khai(kiểm tra độc lập trên dữ liệu của bệnh nhân), tại thời điểm ra quyết định và sau triển khai (giám sát hiệu năng và công bằng theo nhóm). Lý do: AI đưa ra kết quả có thể rất tự tin, còn hậu quả thì sẽ rất nghiêm trọng trực tiếp đến con người.|

### 2. Case study 1 — Epic Sepsis Model: cảnh báo nhiễm khuẩn huyết bỏ sót phần lớn ca bệnh

#### Brief Case

- Tổ chức / sản phẩm AI: Epic Sepsis Model (ESM)
- Thời gian, địa điểm / bối cảnh: Hoa Kỳ. Dữ liệu kiểm định thu thập tại Michigan Medicine từ 06/12/2018 đến 20/10/2019; kết quả công bố ngày 21/06/2021.
- AI được dùng để làm gì: Dự đoán nguy cơ nhiễm khuẩn huyết (sepsis) của bệnh nhân nội trú và tự động phát cảnh báo cho nhân viên y tế khi điểm nguy cơ vượt ngưỡng, giúp phát hiện sớm để can thiệp (ví dụ dùng kháng sinh) kịp thời.
- Vấn đề hoặc sự kiện đáng chú ý: Nghiên cứu kiểm định độc lập cho thấy mô hình hoạt động kém hơn nhiều so với thông tin do nhà cung cấp công bố. Mô hình bỏ sót phần lớn bệnh nhân bị nhiễm khuẩn huyết nhưng vẫn phát cảnh báo cho một tỷ lệ đáng kể bệnh nhân không bị bệnh, gây quá tải cảnh báo (alert fatigue).
- Số liệu có nguồn: AUC của ESM là 0,63 (KTC 95%: 0,62–0,64), thấp hơn mức 0,76–0,83 mà Epic và University of Colorado Health từng báo cáo (JAMA Internal Medicine, bài bình luận kèm theo).
- Nguồn: The Epic Sepsis Model Falls Short—The Importance of External Validation (bài bình luận) — JAMA Internal Medicine — 21/06/2021 — https://jamanetwork.com/journals/jamainternalmedicine/fullarticle/2781313 (số liệu độ nhạy, độ đặc hiệu, PPV, NPV, AUC).
- Phân biệt bằng chứng và nhận định: Nguồn xác nhận: các chỉ số hiệu năng ở trên AUC từ một trung tâm duy nhất (Michigan Medicine); kết luận của tác giả rằng mô hình độc quyền thiếu kiểm định độc lập. Nhận định của tôi / còn chưa rõ: nghiên cứu là hồi cứu và không đo trực tiếp số bệnh nhân tử vong hay bị tổn hại do cảnh báo bị bỏ sót, nên tôi chỉ kết luận được về nguy cơ tác hại, không kết luận được số ca tác hại thực tế. Kết quả từ một bệnh viện học thuật có thể không đại diện cho mọi bệnh viện dùng ESM. Michigan Medicine cũng nêu rằng Epic không "sai" trong phân tích của họ; chênh lệch một phần đến từ cách định nghĩa nhiễm khuẩn huyết và cách loại các cảnh báo xảy ra sau khi bác sĩ đã can thiệp. 

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi bệnh nhân nội trú đang diễn tiến nhiễm khuẩn huyết (giai đoạn sớm, triệu chứng còn mơ hồ) và nhóm điều trị dựa một phần vào việc hệ thống có phát cảnh báo hay không|
| Stakeholder bị ảnh hưởng | Bệnh nhân nhập viện và người nhà; bác sĩ, điều dưỡng, nhà cung cấp phần mềm; bệnh viện |
| Failure mode | Bỏ sót; Cảnh báo giả; Hiệu năng thực tế thấp hơn hiệu năng được công bố.|
| Layer bắt đầu lỗi | Model là layer có bằng chứng rõ nhất (khả năng phân biệt kém khi kiểm định ngoài). Có thêm yếu tố Grounding/đánh giá: mô hình triển khai rộng khi chưa có kiểm định độc lập và định nghĩa nhiễm khuẩn huyết dùng để đánh giá chưa thống nhất. Ở UX, lỗi thể hiện qua alert fatigue. Chưa đủ bằng chứng để khẳng định nguyên nhân gốc nằm ở dữ liệu huấn luyện hay thiết kế mô hình, vì mô hình là độc quyền và ít thông tin công khai.|
| Harm xảy ra là gì? |	Nguy cơ: bệnh nhân nhiễm khuẩn huyết không được cảnh báo có thể được điều trị chậm, làm tăng nguy cơ diễn tiến nặng hoặc tử vong. Hậu quả đã được đo: nhân viên y tế phải xử lý lượng cảnh báo lớn nhưng độ chính xác thấp,tức là gánh nặng vận hành đã xảy ra. |
| Harm lens | Tác hại sức khỏe/an toàn người bệnh; gánh nặng vận hành và độ tin cậy của hệ thống hỗ trợ ra quyết định; thiếu minh bạch của mô hình độc quyền. |
| Severity | Cao: nhiễm khuẩn huyết có thể gây tử vong và mỗi giờ chậm trễ điều trị là quan trọn |
| Scale | Lớn: Michigan Medicine nêu Epic được 56% bệnh viện và hệ thống y tế ở Mỹ sử dụng, và các tác giả nhận định mô hình được triển khai ở rất nhiều bệnh viện.|
| Probability | Cao ở mức nguy cơ bỏ sót: với độ nhạy chỉ 33%, một bệnh nhân nhiễm khuẩn huyết có khả năng lớn không được mô hình cảnh báo. Xác suất gây tổn hại thực tế thấp hơn vì bác sĩ vẫn có thể phát hiện qua thăm khám lâm sàng; nghiên cứu cho thấy ESM chỉ nhận diện thêm 7% bệnh nhân nhiễm khuẩn huyết chưa được dùng kháng sinh kịp thời, tức giá trị bổ sung so với thực hành lâm sàng hiện có là thấp.|
| Frequency | Thường xuyên, lặp lại: cảnh báo được tạo cho 18% số lượt nhập viện, nên cảnh báo giả xảy ra liên tục; sự cố bỏ sót lặp lại mỗi khi có ca nhiễm khuẩn huyết|
| Vì sao? | Mức Severity cao do hậu quả bệnh nặng và khó đảo ngược. Probability và Frequency cao do các chỉ số đo được ngoài thực tế (độ nhạy 33%, PPV 12%, tỷ lệ cảnh báo 18%). Giới hạn bằng chứng: một trung tâm, hồi cứu; không đo (tử vong, thời gian nằm viện); đánh giá phụ thuộc định nghĩa nhiễm khuẩn huyết và cách xử lý cảnh báo muộn.|

### 3. Case study 2 — Thuật toán chấm điểm rủi ro sức khỏe thiên lệch chủng tộc

#### Brief Case

- Tổ chức / sản phẩm AI: Một thuật toán dự đoán rủi ro thương mại, được các hệ thống y tế và bên chi trả ở Mỹ sử dụng rộng rãi để chọn bệnh nhân vào chương trình quản lý chăm sóc nhu cầu phức tạp (high-risk care management). Nhóm nghiên cứu: Ziad Obermeyer (UC Berkeley), Brian Powers, Christine Vogeli, Sendhil Mullainathan.
- Thời gian, địa điểm / bối cảnh: Hoa Kỳ; nghiên cứu công bố trên tạp chí Science số 366, ngày 25/10/2019
- AI được dùng để làm gì: Thuật toán dự đoán chi phí chăm sóc sức khỏe trong tương lai và dùng chi phí.
- Vấn đề hoặc sự kiện đáng chú ý: Với cùng một điểm rủi ro, bệnh nhân da đen bệnh nặng hơn đáng kể so với bệnh nhân da trắng.
- Số liệu có nguồn: Thiên lệch làm giảm số bệnh nhân da đen được xác định cần chăm sóc bổ sung hơn một nửa (mô tả của Science, 25/10/2019). Trong số người được đề xuất vào chương trình, tỷ lệ người da đen chỉ 17,7%, thấp hơn 46,5% ở người da trắng (mức chênh lệch 1/2 của Science là do một lỗi nhỏ trong cách tính của tôi).
- Nguồn: Bản trích dẫn và tóm tắt trên SSRC Just Tech — https://just-tech.ssrc.org/citation/dissecting-racial-bias-in-an-algorithm-used-to-manage-the-health-of-populations/ (số liệu 17,7% và 46,5%).
- Phân biệt bằng chứng và nhận định: Nguồn xác nhận: sự chênh lệch giữa điểm rủi ro và mức độ bệnh theo nhóm chủng tộc; cơ chế là dùng chi phí làm proxy cho nhu cầu sức khỏe; mức tăng từ 17,7% lên 46,5% nếu khắc phục; việc thiết kế lại nhãn dự đoán làm giảm thiên lệch.
Nhận định của tôi / còn chưa rõ: nghiên cứu cho thấy thiên lệch trong việc chọn bệnh nhân vào chương trình chăm sóc, nhưng tôi không có bằng chứng từ nguồn về số ca tử vong hoặc biến chứng cụ thể do không được vào chương trình. Số bệnh nhân bị ảnh hưởng ở mức "hàng triệu" là ước tính của tác giả. Các tác giả nêu thuật toán này điển hình cho cả ngành, nhưng tôi không suy ra được các sản phẩm khác cũng có cùng mức thiên lệch.

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment| Khi hệ thống y tế dùng điểm rủi ro để quyết định bệnh nhân nào được đưa vào chương trình chăm sóc đặc biệt (nguồn lực có hạn); bệnh nhân da đen cùng mức bệnh nhưng điểm thấp hơn nên ít được chọn. |
| Stakeholder bị ảnh hưởng | Bệnh nhân da đen (bị ảnh hưởng trực tiếp); bệnh nhân da trắng (được chọn nhiều hơn tương đối); nhân viên chăm sóc/quản lý chương trình; bệnh viện và bên chi trả bảo hiểm; nhà phát triển thuật toán; xã hội nói chung (củng cố bất bình đẳng y tế sẵn có). |
| Failure mode | Thiên lệch do nhãn/proxy (label bias): mục tiêu huấn luyện là chi phí chứ không phải nhu cầu sức khỏe; chi phí phản ánh bất bình đẳng tiếp cận dịch vụ nên mô hình học lại và khuếch đại bất bình đẳng đó. Mô hình có thể vẫn "chính xác" theo số đo dự đoán chi phí. |
| Layer bắt đầu lỗi | Model (cụ thể là mục tiêu dự đoán/định nghĩa nhãn); có yếu tố Grounding vì dữ liệu chi phí không phản ánh đúng thực tế nhu cầu sức khỏe. Bằng chứng từ nguồn xác nhận cơ chế này và cho thấy khi đổi nhãn thì thiên lệch giảm. |
| Harm xảy ra là gì? | Đã được ghi nhận: Bệnh nhân da đen có cùng điểm rủi ro bệnh nặng hơn bệnh nhân da trắng, tức là bị đánh giá thấp nhu cầu; tỷ lệ được xác định cần chăm sóc bổ sung chỉ 17,7% so với 46,5% nếu không có thiên lệch. <br><br> Chỉ là nguy cơ (nguồn không đo): Hệ quả sức khỏe dài hạn (bệnh nặng hơn, nhập viện, tử vong) do không được vào chương trình chăm sóc. |
| Harm lens | Phân biệt đối xử thuật toán; phân bổ nguồn lực không công bằng (allocation harm); hệ quả sức khỏe cho nhóm yếu thế. |
| Severity | High: Ảnh hưởng đến việc ai được chăm sóc y tế bổ sung, liên quan trực tiếp đến sức khỏe của người bệnh mạn tính; và tác hại mang tính hệ thống, khó nhận ra ở từng ca riêng lẻ.|
| Scale | Rất lớn: Các tác giả cho biết thuật toán điển hình của ngành và ảnh hưởng đến hàng triệu bệnh nhân. |
| Probability | Cao: Thiên lệch đã được đo và có cơ chế nhân quả rõ ràng thay vì là sự cố ngẫu nhiên. |
| Frequency | Liên tục: Thiên lệch lặp lại trong mỗi lần thuật toán chấm điểm và chọn bệnh nhân, đến khi thuật toán được sửa. |
| Vì sao? | Mức Severity, Scale, Probability cao do bằng chứng định lượng từ một nghiên cứu công bố trên Science và cơ chế nguyên nhân rõ. Giới hạn bằng chứng: (a) nghiên cứu dựa trên dữ liệu một hệ thống y tế; (b) không đo trực tiếp hậu quả sức khỏe của việc không được chăm sóc bổ sung; (c) không khẳng định mọi thuật toán cùng loại đều thiên lệch như nhau.|

