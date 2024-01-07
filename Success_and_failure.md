## HutechCTF

File Output:
![[Pasted image 20231231001114.png]]

File Code:
![[Pasted image 20231231001252.png]]

Trước khi thực hiện giải chúng ta phân tích xem flag đã bị mã hóa 
	 Đoạn code trên có vẻ như sử dụng mã hóa AES để mã hóa một flag và sau đó thực hiện một phép XOR giữa key và một giá trị ct nào đó.

Dễ thấy code ta thấy rằng việc mã hóa từ flag -> ciphertext dựa trên hàm encode_FLAG. 
	Hàm encode_FLAG nhận vào một key và một flag, sau đó thực hiện việc mã hóa flag bằng thuật toán AES ở 
	chế độ ECB. Trước khi mã hóa,hàm sử dụng một hàm lambda để thêm 
	padding cho flag sao cho độ dài của nó là bội số của 16. Sau khi mã hóa,kết quả được chuyển thành dạng hex và trả 
	về. 

Sau khi thực hiện quá trình encrypt flag thì key bị làm thay đổi, key thay đổi bằng: 
	key = bytes(x ^ y for x, y in zip(key,ct))
	
		Dòng code này thực hiện phép XOR giữa key và một giá trị 
	ct, sau đó gán lại kết quả vào biến key rồi thực hiện in ra key mới.



Code Decrypt:
![[Pasted image 20231231002740.png]]

Giải thích 1 chút về code này nó hoạt động như nào nhé:

#### Hàm decode_FLAG: 
decode_FLAG nhận hai đối số là key và ciphertext_hex. Trong hàm này:  
- Đầu tiên,tạo một đối tượng cipher sử dụng thuật toán AES 
	với chế độ ECB. (Do code tạo hóa dùng mode này nên mã hóa mình cũng dùng lại)
- Sau đó,chuyển đổi ciphertext_hex từ dạng hex sang dạng byte
- Tiếp theo,giải mã ciphertext bằng cách sử dụng 
	cipher.decrypt(ciphertext). 
- Cuối cùng,unpad kết quả giải mã và chuyển đổi thành chuỗi 
	utf-8 trước khi trả về kết quả.

-Nhưng để ý lại 1 chút thì code của đề cho thì key in ra không phải là key gốc. Key này có được dựa trên key gốc thực hiện phép XOR với ct nên ta phải tìm lại key gốc mới có thể tìm được flag:

#### Hàm xor_bytes:
Hàm xor_bytes nhận hai đối số là b1 và b2, đại diện cho hai chuỗi byte cần thực hiện phép XOR. Trong hàm này:  
- Hàm sử dụng zip được sử dụng để kết hợp từng cặp byte tươngứng từ hai chuỗi byte b1 và b2,sau đó thực hiện phép XOR giữa chúng.
- Sau đó,sử dụng list comprehension để thực hiện phép XOR 
	giữa từng cặp byte và tạo ra một list chứa kết quả.  
- Cuối cùng, nó trả về kết quả dưới dạng một chuỗi byte mới.

Cuối cùng ta thu được:
	+ Flag: HUTECH_CTF{Welcome_to_success_!!!}
	+ Key gốc: b'thisiskeyCrypto2'

ct được gợi ý từ hint: chon hutech chon thanh cong. Nên ct sẽ là : ct = b'chonhutechchonthanhcong' =))))))