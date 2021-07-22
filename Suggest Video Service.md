# Suggest Video Service - Deep Suggestion Engine

## Kiến trúc tổng quan
![Kiến trúc tổng quan](https://i.imgur.com/JyuSs5P.png)

## Các thành phần:
### Deep Learning Service:
- Data collector workers: Collect raw app events. Bao gồm thông tin về lịch sử xem video của user, các tương tác và hành động trong quá trình xem video
- Data standardlization: Job chuẩn hoá, làm sạch raw data. Chuyển hoá các dữ liệu thô thành dữ liệu có cấu trúc và ngữ nghĩa.
- Build dataset: Job build data set từ data đã chuẩn hoá, đánh điểm & label cho dữ liệu, phân chia tập test / training.
- Build model: Build Deep Learning model từ dataset.
- Serving: Model build xong sẽ được deploy & config manually lên server thông qua API internal. Model sẽ được preload trước và index vào trong FAISS - để hỗ trợ query nearest neighbour vector trong n-dimension space. Model được dùng để:
	+ Lấy danh sách video phù hợp với user
	+ Lấy danh sách video tương tự với seed video
	+ Lấy dánh sách user tương đồng với một user

### Data Collecting Service:
- Collect các event cần thiết cho việc suggest video và lưu vào DB, hiện tại gồm:
	+ Event user view video
	+ User implicit interests
	+ Post stats / trending score

### Video Indexing Service:
- Collect thông tin video mới / thay đổi
- Detect label của video dựa vào ngữ cảnh tạo ra và nội dung video
- Index thông tin video và kết quả phân tích vào Elasticsearch

### Suggestion Service:
- Cấu hình nhiều phương pháp tạo suggestion khác nhau cùng với cách phân loại để run A/B test trên production
- Thực hiện logic suggest video cho nhiều ngữ cảnh khác nhau với các thuật toán đã được cấu hình trước trong quá trình A/B test bên trên
- Sinh danh sách candidates:
	- Lựa chọn thuật toán phù hợp với user và theo trọng số A/B test để sinh suggestion, hiện tại có:
		+ Suggest theo sở thích của người dùng (explicit & implicit interests)
		+ Suggest ngẫu nhiên (Diversity)
		+ Suggest theo model deep learning (video phù hợp với user hoặc giống với seed video)
		+ Viral suggestion (Các video mới, trending, hoặc được xem gần đây bởi các user tương tự với user gốc)
	- Lấy các thông tin liên quan để tạo ra danh sách candidates
	- Tạo danh sách candidates theo thuận toán
- Ranking các candidates, các tiêu chí hiện tại:
	- distance: score from ES search or deep learning service
	- popularity: video stats (reacts, comms, shares) score from event bucketing service
	- correlation: video from same page with seed videos, from followed pages or having similar category with seed video
	- diversity: variance + random score
	- freshness: video uploaded recently, coeff differently between categories
	- interested: matched with user implicit / explicit interests
	- penalties: video that have been watched / suggested recently will got penalty score
- Trả về top n videos có điểm số cao nhất

### Report Module:
- Dữ liệu app events collect được sync lên GG BigQuery
- Dữ liệu các video đã suggest, các thuật toán được lựa chọn tương ứng và thông tin context được lưu vào MongoDB và sync lên GG BigQuery
- Report được bên BI tạo và cập nhật daily
> Written with [StackEdit](https://stackedit.io/).
