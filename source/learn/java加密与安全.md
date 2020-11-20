防窃听(密文),防篡改（签名）,防伪造

1. URL编码

URL编码是编码算法，不是加密算法，URL编码的目的是把任意文本数据编码为%前缀表示的文本

使用, URLEncode.encode("d","UTF-8") ,URLDecode.decode("d","UTF-8")

java，中url编码方式要注意一下，主要是针对 空格 字符的编码，会编码为 +号，而现在的编码标准会编码为 %2d，要看服务器有没有兼容这2中情况。


2. Base64编码

Base64是编码算法，不是加密算法

Base64编码的目的是把任意二进制数据编码为文本（长度增加1/3）


如：

bytes("中") 3个字节

16进制

0xe4                  0xb8                 0xad

1 1 1 0 0 1 0 0     1 0 1 1 1 0 0 0 	  1 0 1 0 1 1 0 1

每6位一组

1 1 1 0 0 1  | 0 0 1 0 1 1  | 1 0 0 0 1 0  | 1 0 1 1 0 1

39              0b              22              2d

5               L               i               t 

去 64位映射表 找对应 数值结果为：5Lit 



		String original = "Hello\u00ff编码测试";
		//首先将字符串转换成 字节数组，装载容器 original.getBytes("UTF-8")
		String b64 = Base64.getEncoder().encodeToString(original.getBytes("UTF-8")); //把字节数组编码成字符串
		System.out.println(b64);
		String ori = new String(Base64.getDecoder().decode(b64), "UTF-8");
		System.out.println(ori);



		String original = "Hello\u00ff编码测试";
		//withoutPadding 去除末尾 = 号
		String b64 = Base64.getEncoder().withoutPadding().encodeToString(original.getBytes("UTF-8")); 
		System.out.println(b64);
		String ori = new String(Base64.getDecoder().decode(b64), "UTF-8");
		System.out.println(ori);


由于标准的base64编码在url 中会引起冲突，所以我们在url中使用base64 编码，要使用 Base64.getUrlEncoder(), 解码使用 Base64.getUrlEncoder()


3. MD5 摘要算法


	public static byte[] toMD5(byte[] input) {
		MessageDigest md;
		try {
			md = MessageDigest.getInstance("MD5");
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		md.update(input);
		return md.digest();
	}

	public static void main(String[] args) throws Exception {
		String s = "MD5摘要算法测试";
		//注意MD5的输入是 字节， 而不是 字符串，所以将字符串转为字节数组
		byte[] r = toMD5(s.getBytes("UTF-8"));
		//已16进制的形式打印这个字节数组
		System.out.println(String.format("%032x", new BigInteger(1, r)));
	}



	package com.feiyangedu.sample;

	import java.math.BigInteger;
	import java.security.MessageDigest;

	public class MD5Salt {

		public static byte[] toMD5(byte[] input) {
			MessageDigest md;
			try {
				md = MessageDigest.getInstance("MD5");
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			md.update(input);
			return md.digest();
		}

		public static void main(String[] args) throws Exception {
			String passwd = "helloworld";
			String salt = "Random salt";
			byte[] r = MD5Salt.toMD5((salt + passwd).getBytes("UTF-8"));
			System.out.println(String.format("%032x", new BigInteger(1, r)));
		}

	}


4. sha1

SHA-1摘要算法：输出160bits，20bytes


package com.feiyangedu.sample;

import java.math.BigInteger;
import java.security.MessageDigest;

public class SHA {

	public static byte[] sha1(byte[] input) {
		MessageDigest md;
		try {
			md = MessageDigest.getInstance("SHA-1");
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		md.update(input);
		return md.digest();
	}

	public static void main(String[] args) throws Exception {
		String s = "SHA1摘要算法测试";
		byte[] r = sha1(s.getBytes("UTF-8"));
		//20bytes 用%040x
		System.out.println(String.format("%040x", new BigInteger(1, r)));
	}

}


package com.feiyangedu.sample;

import java.math.BigInteger;
import java.security.MessageDigest;

public class Digest {

	public static byte[] digest(String hashAlgorithm, byte[] input) {
		MessageDigest md;
		try {
			md = MessageDigest.getInstance(hashAlgorithm);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
		md.update(input);
		return md.digest();
	}

	public static void main(String[] args) throws Exception {
		String s = "Java摘要算法测试";
		byte[] input = s.getBytes("UTF-8");
		// 标准算法名称：
		// http://docs.oracle.com/javase/6/docs/technotes/guides/security/StandardNames.html#MessageDigest
		byte[] r1 = digest("MD5", input);
		System.out.println(r1.length + ": " + String.format("%0" + (r1.length * 2) + "x", new BigInteger(1, r1)));
		byte[] r2 = digest("SHA-1", input);
		System.out.println(r2.length + ": " + String.format("%0" + (r2.length * 2) + "x", new BigInteger(1, r2)));
		byte[] r3 = digest("SHA-256", input);
		System.out.println(r3.length + ": " + String.format("%0" + (r3.length * 2) + "x", new BigInteger(1, r3)));
		byte[] r4 = digest("RipeMD160", input);
		System.out.println(r4.length + ": " + String.format("%0" + (r4.length * 2) + "x", new BigInteger(1, r4)));
	}

}


