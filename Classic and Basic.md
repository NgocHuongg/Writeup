## HutechCTF

File Output
  
    *No hint
  
    N= 944277460928218727444425796671228006440681423958756385944259965777648467343805051250778307
    p= 1187132467668222120649135047910661042340315271
    e= 65537
    c= 509304373433095933741721585884760854821638120979366068441534204508880522869949802723478795

File code

    from Crypto.Util.number import getPrime, bytes_to_long
    
    bits = 150
    p = getPrime(bits)
    q = getPrime(bits)
    e = 65537
    N = p*q
    m = 0
    
    with open('flag.txt', 'rb') as f:
        m = bytes_to_long(f.read())
    
    c = pow(m, e, N)
    
    print('p=', p)
    print('e=', e)
    print('N=', N)
    print('c=', c)

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

    from Crypto.Util.number import long_to_bytes
    
    N = 944277460928218727444425796671228006440681423958756385944259965777648467343805051250778307
    p = 1187132467668222120649135047910661042340315271
    q = N // p
    e = 65537
    c = 509304373433095933741721585884760854821638120979366068441534204508880522869949802723478795
    
    phi = (p - 1) * (q - 1)
    d = pow(e, -1, phi)
    
    c_decrypt = pow(c, d, N)
    
    decrypted_message = long_to_bytes(c_decrypt)
    
    print("Flag is:", decrypted_message.decode("utf-8"))


Ta có được flag là: HUTECH_CTF{RSA_is_great_encryption}




