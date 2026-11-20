
## 1. يعني إيه MVC؟

الـ MVC هو Design Pattern بنقسم فيه التطبيق لـ 3 أجزاء، وكل جزء بيكون ليه وظيفة معينة، وده بيساعدنا ننظم الكود ونسهّل التعديل عليه.

- **Model:** مسؤول عن الداتا والقواعد الخاصة بيها، زي الـ Classes والـ Validation والـ Business Logic.
- **View:** ده الجزء اللي المستخدم بيشوفه، وبيكون عبارة عن ملفات `.cshtml` باستخدام HTML و Razor.
- **Controller:** بيستقبل الـ Request، ويتعامل مع الـ Model، وبعد كده بيحدد الـ View أو النتيجة اللي هترجع للمستخدم.

يعني مثلًا لما المستخدم يفتح `/Products`، الـ Routing بيوصل الطلب للـ Controller، والـ Action بتجيب المنتجات من الـ Model، وبعدها تبعتها للـ View علشان تعرضها للمستخدم.

## 2. Actions و Action Results

الـ Action هي عبارة عن Public Method موجودة جوه الـ Controller، وبتكون مسؤولة عن تنفيذ طلب معين.

في شوية شروط مهمة:
- لازم تكون Public.
- مينفعش تكون Static أو Generic.
- ممكن نستخدم `[NonAction]` علشان نخلي Method عامة لكن مينفعش تتنفذ من خلال URL.
- بنستخدم `[HttpGet]` و`[HttpPost]` علشان نحدد نوع الـ HTTP Request.

الـ Action ممكن ترجع أنواع مختلفة من النتائج، زي:

- `View()` علشان ترجع صفحة HTML.
- `Json()` علشان ترجع بيانات بصيغة JSON.
- `Content()` علشان ترجع نص عادي.
- `RedirectToAction()` علشان تنقل المستخدم لـ Action تانية.
- `NotFound()` علشان ترجع Status Code 404.
- `BadRequest()` علشان ترجع 400.
- `Ok()` علشان ترجع 200.
- `File()` علشان ترجع ملف للمستخدم.

وفيه فرق بين إننا نحدد نوع النتيجة، زي `JsonResult`، وبين `IActionResult`.

الـ `IActionResult` مرن أكتر؛ لأنه بيسمح للـ Action إنها ترجع نتائج مختلفة حسب الحالة، زي إنها ترجع `NotFound()` أو `RedirectToAction()` أو `Json()`.

## 3. Middleware

الـ Middleware عبارة عن أجزاء من الكود بتشتغل بالترتيب مع كل Request داخل التطبيق.

الـ Request بيمر على الـ Middlewares واحدة ورا التانية، وبعد تنفيذ الطلب الـ Response بيرجع في الاتجاه العكسي.

فيه 3 طرق أساسية:

- `app.Use()`: بتنفذ كود قبل وبعد الـ Middleware اللي بعدها، وبتقدر تكمل باقي الـ Pipeline باستخدام `next()`.
- `app.Run()`: بتوقف الـ Pipeline ومبتسمحش للـ Middlewares اللي بعدها إنها تشتغل.
- `app.Map()`: بتعمل Branch من الـ Pipeline حسب مسار معين.

ومن أهم الحاجات في الـ Middleware إن الترتيب مهم جدًا.

مثلًا:
1. `UseExceptionHandler()` لمعالجة الأخطاء.
2. `UseStaticFiles()` لتقديم ملفات CSS و JavaScript والصور.
3. `UseRouting()` لتحديد الـ Route المناسب.
4. `UseAuthorization()` للتأكد من صلاحيات الوصول.
5. `MapControllerRoute()` لتحديد مسارات الـ Controllers والـ Actions.

## 4. Routing

الـ Routing مسؤولة عن تحديد الـ Controller والـ Action اللي المفروض يستقبلوا الـ Request بناءً على الـ URL.

الـ Default Route بيكون بالشكل ده:

`{controller=Home}/{action=Index}/{id?}`

وده معناه:
- `controller`: اسم الـ Controller، والافتراضي Home.
- `action`: اسم الـ Action، والافتراضي Index.
- `id?`: رقم أو قيمة اختيارية.

أمثلة:
- `/` هتروح لـ HomeController والـ Index.
- `/Products` هتروح لـ ProductsController والـ Index.
- `/Products/Details/2` هتروح لـ Details وتبعتلها id قيمته 2.

فيه كمان نوع اسمه **Attribute Routing**، وده بنحدد فيه الـ Route مباشرة فوق الـ Controller أو الـ Action باستخدام Attributes زي `[Route]` و`[HttpGet]`.

وممكن نستخدم Routing Constraints علشان نحدد نوع البيانات المقبول في الـ URL، زي:
- `int` للأرقام الصحيحة.
- `min(1)` للقيم الأكبر من أو تساوي 1.
- `alpha` للحروف فقط.
- `length(3,20)` لتحديد طول النص.

ولو القيمة مش مطابقة للـ Constraint، الـ Route مش هيعمل Match.

## 5. Model Binding

الـ Model Binding وظيفته إنه ياخد البيانات اللي جاية من الـ HTTP Request ويحطها تلقائيًا في Parameters بتاعة الـ Action، بدل ما نقرأ كل قيمة بنفسنا.

البيانات ممكن تيجي من أكتر من مصدر:

- `[FromRoute]`: من الـ URL.
- `[FromQuery]`: من الجزء اللي بعد علامة `?` في الـ URL.
- `[FromForm]`: من بيانات الـ Form.
- `[FromHeader]`: من الـ Request Headers.
- `[FromBody]`: من الـ Request Body، زي بيانات JSON.
- `[FromServices]`: من الـ Dependency Injection Container.

ولو عندنا Complex Type، الـ Model Binder بيقدر ينشئ Object ويملأ الـ Properties بتاعته بناءً على أسماء القيم اللي وصلت.

ولو حصلت مشكلة في تحويل البيانات، زي إننا نبعت `age=abc` والـ Parameter المفروض يكون رقم، بيتم إضافة Error في `ModelState` وبتاخد القيمة الافتراضية للنوع، زي 0 بالنسبة لـ `int`.

---

