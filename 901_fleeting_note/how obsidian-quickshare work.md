---  
alias:  
creation_date: 2024-06-08 21:13  
tags: 
related:
- [[source code reading]]

---  
[GitHub - mcndt/obsidian-quickshare: 📝 An Obsidian plugin for sharing encrypted Markdown notes on the web. Zero configuration required.](https://github.com/mcndt/obsidian-quickshare)



主要利用 [Noteshare.space — Securely share your Obsidian notes with one click.](https://noteshare.space) 的 CRUD 服務
然後這個 CRUD 服務也是作者自己搞的!

把內容傳過去，就可以那邊看到自己的 note
作者也自己搞了加密的部分



---

主要是看 BE 的部份


這邊呼叫主要邏輯
- 加密
- 打給第三方服務
```ts


	/**
	 * @param body Markdown file to share.
	 * @returns link to shared note with attached decryption key.
	 */
	public async shareNote(
		body: string,
		options?: ShareNoteOptions
	): Promise<Response> {
        // 這邊把 meta 濾掉
		body = this.sanitizeNote(body);
  
		const jsonPayload: JsonPayload = {
			body: body,
			title: options?.title,
		};

		const stringPayload = JSON.stringify(jsonPayload);
		// 加密
		const { ciphertext, iv, key } = await encryptString(stringPayload);
        // 需要把 iv 和加密後的文字傳過去
		const res = await this.postNote(ciphertext, iv);
		res.view_url += `#${key}`;
		console.log(`Note shared: ${res.view_url}`);
		return res;
	}


```



打給第三方服務
```ts


private async postNote(ciphertext: string, iv: string): Promise<Response> {
	const res = await requestUrl({
		url: `${this._url}/api/note`,  // <- "https://noteshare.space"
		method: "POST",
		contentType: "application/json",
		body: JSON.stringify({
			ciphertext: ciphertext,
			iv: iv,
			user_id: this._userId,
			plugin_version: this._pluginVersion,
			crypto_version: "v3",
		}),
	});

	if (res.status == 200 && res.json != null) {
		const returnValue = res.json;
		returnValue.expire_time = moment(returnValue.expire_time);
		return <Response>returnValue;
	}
	throw Error(
		`Error uploading encrypted note (${res.status}): ${res.text}`
	);
}





```




加密主體
```ts




export async function encryptString(
	plaintext: string,
): Promise<EncryptedString> {
	const key = await generateRandomKey();
	const { ciphertext, iv } = await _encryptString(plaintext, key);
	return {
		ciphertext,
		iv,
		key: masterKeyToString(key).slice(0, 43),
	};
}




export async function encryptString(
	md: string,
	secret: ArrayBuffer,
): Promise<EncryptedData> {
	// string to bytes
	// 因為加密算法只能處理 bytes
	const plaintext = new TextEncoder().encode(md);

	// gen iv, 可以看成是另一層的 secret
	const iv = window.crypto.getRandomValues(new Uint8Array(16));

	// 加密, 這邊使用 AES-GCM 对称加密算法
	const buf_ciphertext: ArrayBuffer = await window.crypto.subtle.encrypt(
		{ name: "AES-GCM", iv: iv },
		await _getAesGcmKey(secret),
		plaintext,
	);

	// bytes to base64 string
	const ciphertext = arrayBufferToBase64(buf_ciphertext);

	return { ciphertext, iv: arrayBufferToBase64(iv) };
}




```




