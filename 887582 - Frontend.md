# Technical Test Result

Farhan Rafly Fahrezi Saepulloh (raflyfahr15@gmail.com) – Code Reviewer

## Project

- Name: 887582 - Frontend
- Status: <b>Accepted</b>

## Summary

- Perhatikan penggunaan git ignore, ketika kamu bermain dengan git repository ada beberapa hal yang sebaiknya tidak perlu di up/push ke dalam repository agar kode yang ada dalam repository menjadi efisien dan tidak terdapat hal yang tidak diperlukan. Beberapa contoh folder dan file yang tidak perlu di masukkan kedalam repository yaitu `dist` yang memiliki hasil build, `e2e` atau folder testing lainnya, `.lock` file dan lainnya.
- Manfaatkan penggunaan git dengan baik dengan melakukan versioning yang jelas. Sehingga ketika ada perubahan code, tidak perlu menyimpannya dengan mengkomentari kode yang lama, hapus saja. Jika memang dibutuhkan kembali tracing melalui commit sebelumnya.
- Pecah kode menjadi beberapa bagian sesuai fungsi dan perannya agar tidak menumpuk pada 1 file. Selain itu, pendekatan yang biasa disebut atomic ini membuat kita lebih mudah melakukan maintenance karena setiap kode terpisah dan memiliki tempatnya masing - masing sehingga kita tidak begitu kesulitan untuk mencari kode yang salah atau ingin diperbaiki.
- Pemanfaatan `try catch` ketika melakukan fetching data agar dapat menghandle error ketika fetching data tersebut gagal dilakukan.

## Code Review

### Architecture

Pecah style menjadi beberapa bagian sesuai fungsi dan perannya agar terlihat lebih baik dan mudah di maintenance. Seperti,

```
/styles
- /component
-- /button
--- index.css
-- /hero
--- index.css
- index.css
index.css
```

Lalu gunakan import pada CSS agar proses pemasangan hanya cukup dengan menambahkan index.css pada `/styles`. Sehingga secara general kode akan terlihat seperti ini,

```css
/* /styles/index.css */

@import url('./component/index.css');
```

```css
/* /styles/component/index.css */

@import url('./button/index.css');
@import url('./button/hero.css');
```

Dengan begitu kita cukup melakukan import 1 file saja pada HTML/JavaScript dan semua style akan terload secara bersamaan, sehingga jika ada perubahan / penambahan style kita hanya perlu memodifikasi pada scope folder `/style` saja.

```html
<html>
  <head>
    <link rel="stylesheet" href="../styles/index.css" />
  </head>
</html>
```

atau jika di JavaScript

```javascript
import '../styles/index.css'
```

Begitu pula dengan struktur file yang lain jadikan menjadi pecahan - pecahan kecil jika memungkinkan dan buatlah 1 import untuk menjadi gerbang pada scope folder tersebut. Selebihnya sudah baik.

### Writing Code

- Manfaatkan penggunaan `try catch` dalam mengkonsumsi data dari pihak ketiga. Contohnya seperti ketika melakukan fetching data dari API menggunakan promise.

  ```javascript
  // src/scripts/data/restaurantdb-source.js

  const response = await fetch(API_ENDPOINT.LIST)
  ```

  Anda bisa optimalkan data fetching disini dengan menggunakan `catch`, hal tersebut bermanfaat untuk melakukan error handling ketika proses fetching data yang dilakukan itu gagal. Sehingga ketika `catch` diimplementasikan akan terlihat seperti ini,

  ```javascript
  const response = await fetch(API_ENDPOINT.LIST).catch(() => {
    return {
      // return something to avoid error
    }
  })
  ```

  Atau juga bisa seperti ini,

  ```javascript
  try {
    const response = await fetch(API_ENDPOINT.LIST)
  } catch (error) {
    // do something when error
  }
  ```

- Hapus penggunaan lines of code yang tidak perlu, gunakan komentar untuk penggunaan petunjuk jangan untuk mendokumentasikan kode lama. Contoh,

  ```javascript
  // Fungsi berikut digunakan untuk abcd ✅
  ```

  ```javascript
  // const myOldFunc = () => {
  //
  // } ❌
  ```

- Dalam penulisan kode diperlukan konsistensi yang baik agar dapat dilihat dan di maintain dengan baik. Salah satunya adalah dengan penggunaan formater seperti [prettier](https://prettier.io/) yang memudahkan kita dalam melakukan formating pada kode sehingga kode akan terlihat sama pada semua file.

- Ketika berkaitan dengan struktur kode yang berulang coba implementasi pendekatan custom element seperti,

  ```javascript
  class MyCustomComponent extends HTMLElement {
    constructor() {
      super()
      this.data = this.getAttribute('data')
    }

    connectedCallback() {
      this.render()
    }

    render() {
      this.innerHTML = `
      <div>
          <h2>${this.data}</h2>
          <!-- Add your component's markup here -->
      </div>
      `
    }
  }

  customElements.define('my-custom-component', MyCustomComponent)
  ```

  Dengan begitu kita memiliki isolated dan scope element yang jelas dan berbagai method yang bisa kita maksimalkan, selain itu dengan mengimplementasikan custom element memudahkan kita mengirimkan data via attribute data yang sangat bermanfaat ketika kita melakukan iterasi dan mengembalikan html beserta data yang berbeda - beda.

- Ketika melakukan import gunakan susunan dari global hingga local agar lebih mudah melakukan klasifikasi scopenya, seperti

  ```javascript
  // dependency import
  import 'idb'
  import 'regenerator-runtime'

  // alias import
  import '@/utils/index.js'
  import '@/component/index.css'

  // local import
  import './helpers.js'
  ```

### Testing

- Karma ✅ <br/>

  ```bash
  # command
  npm run test

  # result
  Chrome 110.0.0.0 (Windows 10): Executed 10 of 10 SUCCESS (0.096 secs / 0.066 secs)
  TOTAL: 10 SUCCESS
  ```

- ESLint ❌ <br/>

  ```bash
  # command
  npm run lint

  # result
  ✖ 9 problems (9 errors, 0 warnings)
  9 errors and 0 warnings potentially fixable with the `--fix` option.
  ```

  Solusinya adalah gunakan flag yang direkomendasikan atau gunakan code formatting yang rules nya disesuaikan dengan rules eslint.

- E2E ❌ <br/>

  ```bash
  # command
  npm run e2e

  # result
  FAIL  | 1 passed, 2 failed   // 17s
  ```

  Cek kembali deskripsi error dan perbaiki kesalahan pada web maupun test scenario.

### UI

Secara overall UI masih nyaman untuk dilihat namun ketika dilakukan pengecilan layar, app ini masih belum sempurna. Contohnya jika dikecilkan navigasinya tidak memiliki behavior seperti yang seharusnya. Ketika tombol pada navigasi sudah tidak muat jadikan hamburger button saja.

![image](https://user-images.githubusercontent.com/33690126/221389779-65a57002-05a7-423e-ae2d-2565cf472477.png)

![image](https://user-images.githubusercontent.com/33690126/221390276-f02ed24b-acc7-45ef-a489-b5a586bee58e.png)

![image](https://user-images.githubusercontent.com/33690126/221390294-9cdce067-858a-4513-bcf7-ef59296934ba.png)

<p align=center>
<img width="278" alt="image" src="https://user-images.githubusercontent.com/33690126/218365960-2477e194-bc93-4440-b0de-8dd8980bb63e.png">
</p>

Ketika sudah jadi hamburger button buat menu menjadi `width: 100%` agar terlihat lebih baik.

<p align=center>
<img width="238" alt="image" src="https://user-images.githubusercontent.com/33690126/218366007-9c910c11-739e-40fd-8934-cadecd5e97a1.png">
</p>
