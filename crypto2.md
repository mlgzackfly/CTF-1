# Part I. Basic
### RSA_000

PicoCTF 2017:compute-rsa-50


### RSA_001
>* PicoCTF 2013: RSA
>* https://github.com/ctfs/write-ups-2013/tree/master/pico-ctf-2013/rsa
```
Math is cool!
Use the RSA algorithm to decode the secret message, c, p, q, and e are parameters for the RSA algorithm.
```
```
p =  9648423029010515676590551740010426534945737639235739800643989352039852507298491399561035009163427050370107570733633350911691280297777160200625281665378483
q =  11874843837980297032092405848653656852760910154543380907650040190704283358909208578251063047732443992230647903887510065547947313543299303261986053486569407
e =  65537
c =  83208298995174604174773590298203639360540024871256126892889661345742403314929861939100492666605647316646576486526217457006376842280869728581726746401583705899941768214138742259689334840735633553053887641847651173776251820293087212885670180367406807406765923638973161375817392737747832762751690104423869019034
```
Use RSA to find the secret message
```
#!/usr/bin/python2
import gmpy2

p =  9648423029010515676590551740010426534945737639235739800643989352039852507298491399561035009163427050370107570733633350911691280297777160200625281665378483
q =  11874843837980297032092405848653656852760910154543380907650040190704283358909208578251063047732443992230647903887510065547947313543299303261986053486569407
e =  65537
c =  83208298995174604174773590298203639360540024871256126892889661345742403314929861939100492666605647316646576486526217457006376842280869728581726746401583705899941768214138742259689334840735633553053887641847651173776251820293087212885670180367406807406765923638973161375817392737747832762751690104423869019034
t = (p-1)*(q-1)
n = p*q

# returns d such that e * d == 1 modulo t, or 0 if no such y exists.
d = gmpy2.invert(e,t)

# Decryption
m = pow(c,d,n)
print "Solved ! m = %d" % m
```


### RSA_002

>* ABCTF 2016 : old-rsa-70

I'm sure you can retrieve the flag from this file.

[HINT] Some good math skills may help.

https://kimiyuki.net/blog/2016/07/23/abctf-2016/
```
步驟一:使用factordb.com 進行大質因數分解
答案如下:
p=238324208831434331628131715304428889871,q=296805874594538235115008173244022912163

步驟二:使用python程式破解

#!/usr/bin/env python3
c = 29846947519214575162497413725060412546119233216851184246267357770082463030225
p = 238324208831434331628131715304428889871
q = 296805874594538235115008173244022912163
n = p * q
e = 3

import gmpy2
from Crypto.PublicKey import RSA
d = lambda p, q, e: int(gmpy2.invert(e, (p-1)*(q-1)))

key = RSA.construct((n, e, d(p,q,e)))
import binascii
print(binascii.unhexlify(hex(key.decrypt(c))[2:]).decode())
```

# RSA_003

>* EKOPARTY PRE-CTF 2015: RSA 2070
>* https://github.com/ctfs/write-ups-2015/tree/master/ekoparty-pre-ctf-2015/crypto/rsa-2070
>* https://0x90r00t.com/2015/09/20/ekoparty-pre-ctf-2015-cry100-rsa-2070-write-up/
>* https://github.com/ByteBandits/writeups/blob/master/ekoparty-pre-ctf-2015/crypto/rsa-2070/chaitan94/README.md


步驟一:這是一個 RSA public key, with a non common size of 2070 bits:

let's get the modulus (n) and exponent (e)

$ openssl rsa -noout -text -inform PEM -in public.key -pubin
```
Public-Key: (2070 bit)
Modulus:
25:b1:8b:f5:f3:89:09:7d:17:23:78:66:bb:51:cf:
f8:de:92:24:53:74:9e:bc:40:3b:09:95:c9:7c:0e:
38:6d:46:c1:61:ca:df:f7:7c:69:86:0d:ae:47:91:
c2:14:cf:84:87:aa:aa:9f:26:e9:20:a9:77:83:49:
06:03:8a:ef:b5:c3:08:27:df:cf:3f:c9:e9:76:95:
44:f9:4e:07:cd:fe:08:72:03:9a:3a:62:62:11:66:
78:b2:61:fb:2d:6b:9d:32:53:9e:92:a1:53:b3:67:
56:29:ba:b3:94:2e:7d:35:e3:0f:7e:ef:5a:bf:1c:
50:d7:97:d0:cc:88:e1:bd:cc:fd:1a:12:ea:6f:7e:
f7:5c:37:27:db:df:2e:78:0f:34:28:ae:8f:7a:4f:
b7:a8:9f:18:4a:36:50:32:b1:53:f8:42:5e:84:57:
50:eb:2b:7a:bc:02:dc:15:ce:02:07:50:7a:a9:50:
86:3b:b8:48:0a:78:02:8d:d6:29:79:94:4d:6c:63:
3f:af:a1:03:e4:db:28:ce:87:f5:a0:c6:ed:4a:2f:
26:64:42:7f:56:5c:77:81:ab:61:91:45:6d:97:1c:
7f:fa:39:52:72:37:4c:ec:01:55:e5:f9:11:89:db:
74:2e:4c:28:b0:3a:0f:a1:1c:ff:b0:31:73:d2:a4:
cc:e6:ae:53
Exponent: 65537 (0x10001)
```
The public key only contains the modulus N and the exponent e.

步驟二:轉成十進位的數字

N could be factorized in 2 prime numbers p and q, so N = p x q.

Based on those 2 numbers we will be able to generate the private key.

As the modulus above is in a hexadecimal form, we converted it in a decimal form:
```
python -c "print int('25b18bf5f389097d17237866bb51cff8de922453749ebc403b0995c97c0e386d46c161cadff77c69860dae4791c214cf8487aaaa9f26e920a977834906038aefb5c30827dfcf3fc9e9769544f94e07cdfe0872039a3a6262116678b261fb2d6b9d32539e92a153b3675629bab3942e7d35e30f7eef5abf1c50d797d0cc88e1bdccfd1a12ea6f7ef75c3727dbdf2e780f3428ae8f7a4fb7a89f184a365032b153f8425e845750eb2b7abc02dc15ce0207507aa950863bb8480a78028dd62979944d6c633fafa103e4db28ce87f5a0c6ed4a2f2664427f565c7781ab6191456d971c7ffa395272374cec0155e5f91189db742e4c28b03a0fa11cffb03173d2a4cce6ae53', 16)"
```
你會得到十進位的數字:

79832181757332818552764610761349592984614744432279135328398999801627880283610900361281249973175805069916210179560506497075132524902086881120372213626641879468491936860976686933630869673826972619938321951599146744807653301076026577949579618331502776303983485566046485431039541708467141408260220098592761245010678592347501894176269580510459729633673468068467144199744563731826362102608811033400887813754780282628099443490170016087838606998017490456601315802448567772411623826281747245660954245413781519794295336197555688543537992197142258053220453757666537840276416475602759374950715283890232230741542737319569819793988431443

步驟三:使用factordb.com 進行大質因數分解

答案如下:7983218175…43 = 3133337 x 2547832606…39

25478326064937419292200172136399497719081842914528228316455906211693118321971399936004729134841162974144246271486439695786036588117424611881955950996219646807378822278285638261582099108339438949573034101215141156156408742843820048066830863814362379885720395082318462850002901605689761876319151147352730090957556940842144299887394678743607766937828094478336401159449035878306853716216548374273462386508307367713112073004011383418967894930554067582453248981022011922883374442736848045920676341361871231787163441467533076890081721882179369168787287724769642665399992556052144845878600126283968890273067575342061776244939

步驟四:產生私鑰private key==>使用rsatool工具模組

語法格式: ./rsatool.py -p primeP -q primeQ -o outputFile

./rsatool.py -p 3133337 -q 25478326064937419292200172136399497719081842914528228316455906211693118321971399936004729134841162974144246271486439695786036588117424611881955950996219646807378822278285638261582099108339438949573034101215141156156408742843820048066830863814362379885720395082318462850002901605689761876319151147352730090957556940842144299887394678743607766937828094478336401159449035878306853716216548374273462386508307367713112073004011383418967894930554067582453248981022011922883374442736848045920676341361871231787163441467533076890081721882179369168787287724769642665399992556052144845878600126283968890273067575342061776244939 -o priv.key


python2 rsatool.py -f PEM -o private.pem -p 25478326064937419292200172136399497719081842914528228316455906211693118321971399936004729134841162974144246271486439695786036588117424611881955950996219646807378822278285638261582099108339438949573034101215141156156408742843820048066830863814362379885720395082318462850002901605689761876319151147352730090957556940842144299887394678743607766937828094478336401159449035878306853716216548374273462386508307367713112073004011383418967894930554067582453248981022011922883374442736848045920676341361871231787163441467533076890081721882179369168787287724769642665399992556052144845878600126283968890273067575342061776244939 -q 3133337

步驟五:使用openssl解密==>殘念

base64 -d flag.enc | openssl rsautl -decrypt -inkey private.pem

步驟五:使用pycrypto解密

python2 rsa_decrypt.py flag.enc private.pem

EKO{classic_rsa_challenge_is_boring_but_necessary}