# React code standards

Энэхүү documentation нь react app хөгжүүлэхэд ямар стандарт баримтлавал зохих талаар байх болно. 

## 1. Using typescipt
**Яагаад Typescript гэж?**

Тухайн төсөл нь том хэмжээний, урт хугацаанд хөгжүүлэгдэх, олон хүн хамтарч хөгжүүлдэг мөн цаашид илүү том болох төсөл бол **Typescript** ашиглах нь зүйтэй. **Typescript** нь **Javascript** дээр суурилсан type checking хэл юм. Жишээ нь:

**Javascript**
```js
formatMoney('10000₮');
//output N/A
```

**Typescipt**
```ts
formatMoney('1000₮');
```

Ийм тохиолдолд compile-аас өмнө **Type 'string' is not assignable to type 'number'** гэж алдаа өгнө. Ингэснээр уг алдааг засаж байж project ажиллах учир **production** болон бусад орчинд ийм алдаа гарахаас сэргийлэх юм.

## 2. Eslint ашиглах 

**https://eslint.org/**

Eslint нь Javasript болон Typescript file-ийн syntax check, зохиосон rule-ийн дагуу эсэхийг мөн бичиглэлийн style шалгадаг tool юм. Давуу тал нь syntax болон style-ийг өөрийн хүссэнээр rule зохиож хэрэгжүүлэх боломжтой. 

Жишээ нь манай төсөл **single quotes** ашиглана гэвэл

```json
{
  "rules": {
    "quotes": ["off", "single"]
  }
}
```
Заралсан ч хэрэглэхгүй байгаа хувьсагчийг алдаа гэж үзнэ гэвэл

```json
{
  "rules": {
    "no-unsed-vars": "error"
  }
}
```

Огт **console** ашиглавал warning өгнө гэвэл

```json
{
  "rules": {
    "no-console": "warn"
  }
}
```

Гэх мэт хүссэнээрэй тохируулах боломжтой. Ингэснээр нэг бичиглэлээр багийн бүх гишүүд бичихээс гадна шинээр орсон хүн ч гэсэн яг ижил бичиглэлээр бичнэ гэсэн үг.

### 3. Prettier ашиглах

**https://prettier.io/**

Энэ нь **Eslint** шиг tool бөгөөд Eslint-ийн дагуу кодыг хэлбэржүүлдэг tool юм.

![Prettier tool](https://res.cloudinary.com/practicaldev/image/fetch/s--4KCvB05b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/vrn2j6ewerdkpbuyrmz8.gif)

### 4. Husky lint-staged

**https://typicode.github.io/husky/#/**

Husky Lint-staged tool нь маш чухал tool юм. Энэ tool нь git stage өөрчлөгдөхөд автоматаар ажилладаг Hook юм. Юунд ашиглаж болох вэ гэвэл 

```command
git commit -m "Commit text here"
```

Энэ үед pre-commit Hook ажиллуулж болдог ба үүнийг ашиглан хүссэн command урьдчилж ажиллуулж болох юм. Жишээ нь дээрх command ажиллахаас өмнө **lint** check хийгээд давхар **test** ажиллуулж болох юм. Хэрэв аль нэг дээр нь унавал code push хийгдэхгүй stage буцдаг гэсэн үг.

Дээрх tool-үүдийг тохируулсан жишээ project эндээс харах боломжтой.

**https://github.com/glaadaa/my-custom-boilerplate**
