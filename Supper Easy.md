### HutechCTF

File output: output_debai.txt

File code:

    import string
    from Crypto.Util.number import getPrime
    import random
    
    LOWERCASE_OFFSET = ord("a")
    ALPHABET = string.ascii_lowercase
    bits = 1024
    
    def crypt_two(text,p,q):
    	for i in text:
    		m = ord(i)
    		N = q*p
    		e = 65537
    		c = pow(m,e,N)
    		print('enc =',c)
    		
    def shift(c, k):
    	shifted = ""
    	for char in c:
    		if char.isalnum():
    			is_upper = char.isupper()
    			char = chr((ord(char) - key - ord('A' if is_upper else 'a')) % 26 + ord('A' if is_upper else 'a')) if char.isalpha() else chr((ord(char) - key - ord('0')) % 10 + ord('0'))
    			shifted += char
    		else:
    			shifted += char
    	return shifted
    	
    flag = "redacted"
    key = "redacted"
    assert key>0 and key<10
    
    p = getPrime(bits)
    q = getPrime(bits)
    
    phi = (p-1)*(q-1)
    sum = p+q
    enc = shift(flag,key)
    result = crypt_two(enc,p,q)
    
    print("(p-1)*(q-1) =",phi)
    print("p+q =",sum)

- Đọc và phân tích code nhé:
Step 1. Tạo hai số nguyên tố p và q có độ dài 1024 bit sử dụng thư viện Crypto.Util.number.

Step 2. Tính giá trị của phi và sum dựa trên p và q.

Step 3. Mã hóa chuỗi flag sử dụng hàm shift với một khóa được xác định trước.

Step 4. Sử dụng hàm crypt_two để mã hóa chuỗi đã được dịch chuyển với p và q.

Step 5. In ra kết quả của phi và sum.

<Nói nhanh gọn lẹ thì flag sẽ áp dụng mã hóa cổ điển Ceaser Cipher để dịch thành 1 đoạn kí tự mới, đoạn kí tự vừa nhập đem đi mã hóa bằng RSA>

- Đã có phi = (p -1)*(q - 1) và sum = p + q nên ta dễ dàng tính ra p và q ha ** Do mình lười code trên kali linux nên mình nhờ người khác tính hộ rồi lấy giá trị p và q luôn =))) **

- Code Decrypt:


      import string
  
      phi = 14571835957843094858730517099895549810974365393908524582053196159508600475188530848603955437945909978493130722970624838796667794002274368353495704892076782116209974916718822895262051339735188530957531461718738582995318348478275264788890677852001388730482783352628072333703287848959397750121058273967912069854637461804673169390335929258354870481549988051110077902680224862845717023745451373631043584973250521879327856504448789897692337953669887586763860997069757939765218792925730825888322021151993673190949930774574521080833892483524881928561346101158639581107673244897204839045180356442753080920502119162564702687000
      sum_PandQ = 244735044990481028207725958343842837873763537462416248483090414242315616016598090518719683400172729315225836542978673147262430851842240536637182421662864145536279513508412173449468065948278635087184310963847941409605738430868812375954721932828552796986774085581978841090038433425232301473242475559650829554212

      LOWERCASE_OFFSET = ord("a")
      ALPHABET = string.ascii_lowercase
  
      def reverse_shift(c, k):
          FLAG = ""
          for char in c:
              if char.isalnum():
                  is_upper = char.isupper()
                  char = chr((ord(char) + k - ord('A' if is_upper else 'a')) % 26 + ord('A' if is_upper else 'a')) if char.isalpha() else chr((ord(char) + k - ord('0')) % 10 + ord('0'))
                  FLAG += char
              else:
                  FLAG += char
          return FLAG
      
      with open(r'Decrypt CTF\output_debai.txt', 'r') as file:
          data = file.read()
      
      enc = list(map(int, data.split(',')))
      
      p = 102318218173641263254604113608066946841312009884655273426067593145556785254344208933184795781442916828781899277511700576271151082167406970722401609996351482629268897716073818061981632039611951884442310407927088597647708380663283443409558449421308491486884304305203112538754656809085445989273499522482692714261
      q = 142416826816839764953121844735775891032451527577760975057022821096758830762253881585534887618729812486443937265466972570991279769674833565914780811666512662907010615792338355387486433908666683202742000555920852811958030050205528932545163483407244305499889781276775728551283776616146855483968976037168136839951
      e = 65537
      
      N = p * q
      d = pow(e, -1, phi)
      
      def decrypt_and_combine(enc, d, N):
          ciphertext = ""
          for value in enc:
              decrypted_value = pow(value, d, N)
              decrypted_char = chr(decrypted_value)
              ciphertext += decrypted_char
          return ciphertext
      
      ciphertext = decrypt_and_combine(enc, d, N)
      
      for k in range(1, 10):
              plaintext = reverse_shift(ciphertext, k)
              print(plaintext)


- Các bạn nhớ sửa đường dẫn vào file nhé. File output cũng phải sửa (Sửa bằng cách: xóa phi, sum, "enc =." Sau đó thêm dấu ',' vào mỗi cuối 1 giá trị enc đến hết nhé)

- Giờ thì giải thích sao mình lại làm vậy nha:
  + Mình cho python đọc cái file rồi lưu tất cả các dữ liệu có bên trong thành dạng 1 list ( Mình lưu thành list để nó mặc định kiểu dữ liệu các phần tử thuộc kiểu int luôn cho dễ làm và chatGPT nó gợi ý như thế :>> )
    
- Do chương trình được tạo mã theo thứ tự Ceaser Cipher -> RSA nên giờ mình làm từ RSA đến Ceaser Cipher nhé:
  + Hàm để decrypt_and_combine để decrypt RSA. Mình decrypt các các giá trị có trong list enc rồi đổi nó về kí tự tương ứng
  
- Do đề bài set key assert key>0 and key<10 nên key sẽ là key >= 1 và k <= 9 (key ở đây là độ dịch chuyển của hàm shift ấy). Khúc này cũng không có gì đáng nói nhiều ha
      + Cho key chạy từ 1 đến 9, sau đó thực hiện việc gọi hàm reverse_shift chạy lấy lại plaintext

  - Do in được quá nhiều plaintext nên phải lựa xem cái nào đúng, đem từng cái lên CyberChef xem coi cái nào chuyển được từ Base64 về Ascii là cái đó là đúng
 
  - Khi đó ta thu được flag là: HUTECH_CTF{SFVURUNIX0NURntUaGlzX2lzX2Zha2VfZmxhZzo8fQ==}
        + Câu này không in ra được Form flag HUTECH_CTF nên mình phải tự thêm vào 

  
