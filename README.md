🏨 Hotel Recommendation System
📌 Giới thiệu
Dự án này xây dựng hệ thống gợi ý khách sạn dựa trên đánh giá khách hàng và thông tin khách sạn.
Các kỹ thuật áp dụng gồm:
•	Cosine Similarity (TF-IDF trên corpus)
•	Content-based Filtering (Gensim)
•	Collaborative Filtering (ALS - Spark MLlib)
•	Insight Business cho khách sạn
________________________________________
🧹 Data Cleaning
1️⃣   Bảng Hotel_info
•	Fill null: Total Score, Location, Cleanliness, Service, Facilities, Value for money, Comfort = 0.
•	Tách Hotel_Rank → Hotel_Rank_Num để lấy số sao.
•	Fill Hotel_Description = "-" nếu null.
2️⃣   Bảng Hotel_comment
•	Đổi Score từ object → float.
•	Drop hàng có Reviewer_Name hoặc Body null.
•	Tách Stay_Detail → Days, Month_Stay.
•	Chuyển Review_Date sang datetime.
•	Tính Mean_Reviewer_Score cho từng Hotel_ID.
•	Merge Hotel_Name từ Hotel_info.
•	Drop hàng không có Hotel_Name.
•	Tạo Review_ID_Real = Hotel_ID + Reviewer_Name + Review_Date.
•	Làm sạch Body: xóa từ sai, stopword, teencode, emoji, tiếng Anh.
✅ Dữ liệu từ 80,168 dòng → 25,906 dòng.
________________________________________
📊 Recommendation Methods
2️⃣  Cosine Similarity
•	Tạo bảng Hotel_corpus từ merge Hotel_info + Hotel_comment, gồm:
Hotel_ID, Hotel_Name, Hotel_Address, Hotel_Description, Room_Type, Body_Clean.
•	Tạo cột Content bằng cách ghép các cột trên.
•	Loại bỏ duplicate Content: dữ liệu từ 25,906 dòng → 2,088 dòng.
•	Dùng TF-IDF + Cosine Similarity để tính khoảng cách giữa query (từ khóa) và khách sạn.
________________________________________
3️⃣  Gensim
•	Tạo bảng Hotel_corpus tương tự Cosine.
•	Tạo cột Content_wt để làm Dictionary + Corpus cho Gensim.
•	Huấn luyện mô hình Doc2Vec hoặc Word2Vec.
•	Khi nhập từ khóa → vector hóa → tìm khách sạn gần nhất trong không gian embedding.
________________________________________
4️⃣   Collaborative Filtering (ALS)
•	Tạo bảng gồm:
Nationality, Hotel_ID, Score, Hotel_Description, Body_clean.
•	Encode lại:
o	Nationality_id (mapping từ Nationality).
o	hotel_numeric_id (mapping từ Hotel_ID).
•	Input cho ALS:
o	userCol="user_numeric_id",
o	itemCol="hotel_numeric_id",
o	ratingCol="Score".
•	Train/test split (80/20).
•	Huấn luyện ALS và đánh giá bằng RMSE.
________________________________________
📈 Insight Business
Hệ thống có 2 hàm chính:
1.	Hàm tìm khách sạn theo ID hoặc keyword
o	Trả về bảng gợi ý khách sạn kèm theo điểm đánh giá trung bình.
o	Hiển thị biểu đồ phân tích khách hàng (quốc tịch, nhóm khách, xu hướng theo thời gian).
2.	Hàm dành cho chủ khách sạn
o	Truy xuất theo Hotel_ID.
o	Trả ra thống kê chi tiết:
	Phân bố quốc tịch khách hàng.
	Phân nhóm khách (cặp đôi, gia đình, công tác, solo).
	Xu hướng theo thời gian (số review / điểm số trung bình).
________________________________________
📂 Kết quả
•	Cosine: gợi ý khách sạn dựa trên TF-IDF của toàn bộ content.
•	Gensim: gợi ý khách sạn dựa trên ngữ nghĩa sâu hơn từ review + mô tả.
•	ALS: gợi ý cá nhân hóa theo user (Nationality) và lịch sử chấm điểm.
•	Insight Business: cung cấp dashboard phân tích cho khách sạn → hỗ trợ chiến lược marketing.