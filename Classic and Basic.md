## HutechCTF

File Output
![[Pasted image 20231226180812.png]]
File code
![[Pasted image 20231226180859.png]]

Trong hệ mã RSA, ta có số Module (N) được tính toán bằng công thức N = p * q. Khi đó từ file output ta dễ dàng tính toán được số q
> q = N//p

Tiếp theo để giải mã được thông điệp ta cần tính giá trị Phi của N
> phi = (p - 1) * (q - 1)

Sau đó ta cần tìm d, d là nghịch đảo modulo của e
> d = pow(e, -1, phi)

Như vậy ta đã có đủ dữ liệu của public key và private key, ta tiến hành thực hiện giải mã thông điệp

Plaintext m được tính theo công thức sau đây
> m = c^d mod N

Code decrypt:
![[Pasted image 20231226183327.png]]

Ta có được flag là: HUTECH_CTF{RSA_is_great_encryption}




