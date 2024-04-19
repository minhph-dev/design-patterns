# Design Patterns JS

1. **[Creational](#creational)**

   - Cung cấp các cơ chế tạo đối tượng giúp tăng tính linh hoạt và tái sử dụng mã hiện có.
   - Xử lý việc tạo (xây dựng các đối tượng)
   - Rõ ràng (hàm tạo) v/s ẩn (DI, phản chiếu, v.v.)
   - Bán buôn (câu lệnh đơn) v/s từng phần (từng bước)

   * [Builder](#builder)
   * [Factory](#factory)
   * [Abstract Factory](#abstract-factory)
   * [Prototype](#prototype)
   * [Singleton](#singleton)

2. **[Structural](#structural)**

   - Giải thích cách tập hợp các đối tượng và lớp thành các cấu trúc lớn hơn, đồng thời giữ cho các cấu trúc đó linh hoạt và hiệu quả.
   - Quan tâm đến cấu trúc (cựu thành viên lớp)
   - Các mẫu này là các trình bao bọc bắt chước giao diện lớp cơ bản
   - Nhấn mạnh tầm quan trọng của thiết kế API tốt

   * [Adapter](#adapter)
   * [Bridge](#bridge)
   * [Composite](#composite)
   * [Decorator](#decorator)
   * [Facade](#facade)
   * [Flyweight](#flyweight)
   * [Proxy](#proxy)

3. **[Behavioral](#behavioral)**

   - Đảm bảo việc giao tiếp hiệu quả và phân công trách nhiệm giữa các đối tượng.
   - Tất cả đều khác nhau, không có chủ đề trung tâm.
   - Họ là duy nhất theo những cách đó.

   * [Chain Of Responsibility](#chain-of-resp)
   * [Command](#command)
   * [Interpreter](#interpreter)
   * [Iterator](#iterator)
   * [Mediator](#mediator)
   * [Memento](#memento)
   * [Observer](#observer)
   * [State](#state)
   * [Strategy](#strategy)
   * [Template](#template)
   * [Visitor](#visitor)

## creational

1. ### Builder

   - Thành phần riêng biệt khi việc xây dựng đối tượng trở nên quá phức tạp.
   - Có thể tạo ra những người xây dựng phụ hợp tác lẫn nhau
   - Thường có giao diện trôi chảy

```js
// Employees
var sue = new PersonBuilder("Sue").makeEmployee().makeManager(60).build();
var bill = new PersonBuilder("Bill").makeEmployee().makePartTime().build();
var phil = new PersonBuilder("Phil").makeEmployee().build();

// Shoppers
var charles = new PersonBuilder("Charles")
  .withMoney(500)
  .withShoppingList(["jeans", "sunglasses"])
  .build();

var tabbitha = new PersonBuilder("Tabbitha").withMoney(1000).build();

console.log(sue.toString());
console.log(charles.toString());
```

```js
class PersonBuilder {
  constructor(name) {
    this.name = name;
  }

  makeEmployee() {
    this.isEmployee = true;
    return this;
  }

  makeManager(hours = 40) {
    this.isManager = true;
    this.hours = hours;
    return this;
  }

  makePartTime(hours = 20) {
    this.hours = hours;
    return this;
  }

  withMoney(money) {
    this.money = money;
    return this;
  }

  withShoppingList(list = []) {
    this.shoppingList = list;
    return this;
  }

  build() {
    return new Person(this);
  }
}

class Person {
  constructor(builder) {
    this.name = builder.name;
    this.isEmployee = builder.isEmployee;
    this.isManager = builder.isManager;
    this.hours = builder.hours || 0;
    this.money = builder.money || 0;
    this.shoppingList = builder.shoppingList || [];
  }

  toString() {
    return JSON.stringify(this);
  }
}
```

2. ### Factory
   - Phương thức Factory biểu cảm hơn hàm tạo
   - Một lớp riêng biệt với phương thức xuất xưởng là Facotry
   - Hệ thống phân cấp lớp có thể có hệ thống phân cấp tuyển dụng tương ứng của các nhà máy (Nhà máy trừu tượng)

```js
const bmwX5 = BmwFactory.create("X5");
const bmwX6 = BmwFactory.create("X6");
```

```js
class BmwFactory {
  static create(type) {
    if (type === "X5") return new Bmw(type, 108000, 300);
    if (type === "X6") return new Bmw(type, 111000, 320);
  }
}

class Bmw {
  constructor(model, price, maxSpeed) {
    this.model = model;
    this.price = price;
    this.maxSpeed = maxSpeed;
  }
}
```

3. ### Abstract Factory

```js
const kindOfDroid = "battle";
const myDroidFactory = droidProducer(kindOfDroid);
const myDroid = myDroidFactory();
console.log(myDroid.info()); // Sẽ in ra: "B1, Battle Droid"
```

```js
function droidProducer(kind) {
  if (kind === "battle") return battleDroidFactory;
  return pilotDroidFactory;
}

function battleDroidFactory() {
  return new B1();
}

function pilotDroidFactory() {
  return new Rx24();
}

class B1 {
  info() {
    return "B1, Battle Droid";
  }
}

class Rx24 {
  info() {
    return "Rx24, Pilot Droid";
  }
}
```

4. ### Prototype
   - Tạo đối tượng từ đối tượng hiện có
   - Yêu cầu bản sao sâu rõ ràng hoặc sao chép thông qua tuần tự hóa
   - Công việc bổ sung cần thiết để bảo tồn loại

```js
// Tạo một đối tượng Sheep ban đầu
const originalSheep = new Sheep("Dolly", 50);

// Sao chép đối tượng ban đầu để tạo ra một đối tượng mới
const clonedSheep = originalSheep.clone();

// Kiểm tra xem đối tượng đã được sao chép có giống như đối tượng ban đầu không
console.log(clonedSheep.name); // Kết quả sẽ là 'Dolly'
console.log(clonedSheep.weight); // Kết quả sẽ là 50
```

```js
class Sheep {
  constructor(name, weight) {
    this.name = name;
    this.weight = weight;
  }

  clone() {
    return new Sheep(this.name, this.weight);
  }
}
```

5. ### Singleton
   - Khi bạn cần đảm bảo chỉ tồn tại một phiên bản duy nhất
   - Có thể trả về cùng một đối tượng từ hàm tạo trong mỗi cuộc gọi
   - Sự phụ thuộc trực tiếp vào Singleton là nguy hiểm

```js
// Tạo một thể hiện của lớp Person
const person1 = new Person();

// Thử tạo một thể hiện khác của lớp Person
const person2 = new Person();

// Kiểm tra xem cả hai thể hiện có cùng một tham chiếu không
console.log(person1 === person2); // Kết quả sẽ là true
```

```js
class Person {
  constructor() {
    if (typeof Person.instance === "object") {
      return Person.instance;
    }
    Person.instance = this;
    return this;
  }
}
```

## structural

6. ### Adapater

   - Chuyển đổi giao diện bạn nhận được sang giao diện bạn cần.

```js
// Tạo một đối tượng Soldier với level là 5
const soldier = new Soldier(5);

// Gọi phương thức attack() của Soldier
console.log(`Soldier attacks with power: ${soldier.attack()}`);

// Tạo một đối tượng Jedi với level là 3
const jedi = new Jedi(3);

// Gọi phương thức attackWithSaber() của Jedi
console.log(`Jedi attacks with saber with power: ${jedi.attackWithSaber()}`);

// Tạo một adapter cho Jedi
const jediAdapter = new JediAdapter(jedi);

// Gọi phương thức attack() của Jedi thông qua adapter
console.log(`Jedi (via adapter) attacks with power: ${jediAdapter.attack()}`);
```

```js
class Soldier {
  constructor(level) {
    this.level = level;
  }

  attack() {
    return this.level * 1;
  }
}

class Jedi {
  constructor(level) {
    this.level = level;
  }

  attackWithSaber() {
    return this.level * 100;
  }
}

class JediAdapter {
  constructor(jedi) {
    this.jedi = jedi;
  }

  attack() {
    return this.jedi.attackWithSaber();
  }
}
```

7. ### Bridge

   - Tách rời sự trừu tượng khỏi việc thực hiện

```js
// Tạo một mực acrylic
const acrylicInk = new AcrylicInk();

// Tạo một máy in Epson sử dụng mực acrylic
const epsonPrinter = new EpsonPrinter(acrylicInk);

// In ra thông tin của máy in Epson
console.log(epsonPrinter.print()); // Output: Printer: Epson, Ink: acrylic-based

// Tạo một mực alcohol
const alcoholInk = new AlcoholInk();

// Tạo một máy in HP sử dụng mực alcohol
const hpPrinter = new HPprinter(alcoholInk);

// In ra thông tin của máy in HP
console.log(hpPrinter.print()); // Output: Printer: HP, Ink: alcohol-based
```

```js
class Printer {
  constructor(ink) {
    this.ink = ink;
  }
}

class Ink {
  constructor(type) {
    this.type = type;
  }
  get() {
    return this.type;
  }
}

class EpsonPrinter extends Printer {
  constructor(ink) {
    super(ink);
  }

  print() {
    return "Printer: Epson, Ink: " + this.ink.get();
  }
}

class HPprinter extends Printer {
  constructor(ink) {
    super(ink);
  }

  print() {
    return "Printer: HP, Ink: " + this.ink.get();
  }
}

class AcrylicInk extends Ink {
  constructor() {
    super("acrylic-based");
  }
}

class AlcoholInk extends Ink {
  constructor() {
    super("alcohol-based");
  }
}
```

8. ### Composite

   - Cho phép khách hàng xử lý các đối tượng riêng lẻ và các thành phần của đối tượng một cách thống nhất

```js
// Tạo các đối tượng lá
const floppyDisk = new FloppyDisk();
const hardDrive = new HardDrive();
const memory = new Memory();

// Tạo đối tượng hợp
const cabinet = new Cabinet();

// Thêm các đối tượng lá vào đối tượng hợp
cabinet.add(floppyDisk);
cabinet.add(hardDrive);
cabinet.add(memory);

// Lấy giá của tất cả các thiết bị trong tủ
const totalPrice = cabinet.getPrice();
console.log("Total price of all equipment in the cabinet:", totalPrice);
```

```js
class Equipment {
  getPrice() {
    return this.price || 0;
  }

  getName() {
    return this.name;
  }

  setName(name) {
    this.name = name;
  }
}

// --- composite ---
class Composite extends Equipment {
  constructor() {
    super();
    this.equipments = [];
  }

  add(equipment) {
    this.equipments.push(equipment);
  }

  getPrice() {
    return this.equipments
      .map((equipment) => {
        return equipment.getPrice();
      })
      .reduce((a, b) => {
        return a + b;
      });
  }
}

class Cabinet extends Composite {
  constructor() {
    super();
    this.setName("cabinet");
  }
}

// --- leafs ---
class FloppyDisk extends Equipment {
  constructor() {
    super();
    this.setName("Floppy Disk");
    this.price = 70;
  }
}

class HardDrive extends Equipment {
  constructor() {
    super();
    this.setName("Hard Drive");
    this.price = 250;
  }
}

class Memory extends Equipment {
  constructor() {
    super();
    this.setName("Memory");
    this.price = 280;
  }
}
```

9. ### Decorator

   - Đính kèm các trách nhiệm bổ sung vào đối tượng mà không sửa đổi các đối tượng đó hoặc kế thừa từ chúng. Các trang trí có thể kết hợp với nhau

```js
// Tạo một đối tượng Penne
const penne = new Penne();

// Tạo một đối tượng Penne với sauce
const penneWithSauce = new SauceDecorator(penne);

// Tạo một đối tượng Penne với cheese và sauce
const penneWithCheeseAndSauce = new CheeseDecorator(penneWithSauce);

// Lấy giá của các loại pasta
console.log(penne.getPrice()); // Kết quả sẽ là 8
console.log(penneWithSauce.getPrice()); // Kết quả sẽ là 13 (8 + 5)
console.log(penneWithCheeseAndSauce.getPrice()); // Kết quả sẽ là 16 (8 + 5 + 3)
```

```js
class Pasta {
  constructor() {
    this.price = 0;
  }
  getPrice() {
    return this.price;
  }
}

class Penne extends Pasta {
  constructor() {
    super();
    this.price = 8;
  }
}

class PastaDecorator extends Pasta {
  constructor(pasta) {
    super();
    this.pasta = pasta;
  }

  getPrice() {
    return this.pasta.getPrice();
  }
}

class SauceDecorator extends PastaDecorator {
  constructor(pasta) {
    super(pasta);
  }

  getPrice() {
    return super.getPrice() + 5;
  }
}

class CheeseDecorator extends PastaDecorator {
  constructor(pasta) {
    super(pasta);
  }

  getPrice() {
    return super.getPrice() + 3;
  }
}
```

10. ### Facade

    - Cung cấp một giao diện thống nhất duy nhất trên một tập hợp các hệ thống/giao diện

```js
// Tạo một đối tượng ShopFacade
const shopFacade = new ShopFacade();

// Tính toán giá cuối cùng của một mặt hàng có giá 100
const finalPrice = shopFacade.calc(100);

console.log(finalPrice); // Kết quả sẽ là 99.5 (10% giảm giá, +5 phí vận chuyển, *1.05 phí)
```

```js
class ShopFacade {
  constructor() {
    this.discount = new Discount();
    this.shipping = new Shipping();
    this.fees = new Fees();
  }

  calc(price) {
    price = this.discount.calc(price);
    price = this.fees.calc(price);
    price += this.shipping.calc();
    return price;
  }
}

class Discount {
  calc(value) {
    return value * 0.9;
  }
}

class Shipping {
  calc() {
    return 5;
  }
}

class Fees {
  calc(value) {
    return value * 1.05;
  }
}
```

11. ### Flyweight

    - Kỹ thuật tiết kiệm bộ nhớ. Hỗ trợ hiệu quả số lượng lớn các đối tượng tương tự:

```js
let factory = new colorFactory();

let red = factory.create("red");
let blue = factory.create("blue");
let green = factory.create("green");
```

```js
class Color {
  constructor(name) {
    this.name = name;
  }
}

class colorFactory {
  constructor(name) {
    this.colors = {};
  }
  create(name) {
    let color = this.colors[name];
    if (color) return color;
    this.colors[name] = new Color(name);
    return this.colors[name];
  }
}
```

12. ### Proxy

    - Cho phép bạn cung cấp một đối tượng đại diện hoặc placeholder cho một đối tượng khác để kiểm soát quyền truy cập vào nó.

```js
const driver1 = new Driver(20);
const driver2 = new Driver(15);

const carProxy1 = new CarProxy(driver1);
const carProxy2 = new CarProxy(driver2);

console.log(carProxy1.drive()); // Output: "driving"
console.log(carProxy2.drive()); // Output: "too young to drive"
```

```js
class Car {
  drive() {
    return "driving";
  }
}

class CarProxy {
  constructor(driver) {
    this.driver = driver;
  }
  drive() {
    return this.driver.age < 18 ? "too young to drive" : new Car().drive();
  }
}

class Driver {
  constructor(age) {
    this.age = age;
  }
}
```

## behavioral

13. ### Chain of Responsibility
    - Cho phép các thành phần xử lý thông tin/sự kiện theo chuỗi
    - Mỗi phần tử trong chuỗi đề cập đến phần tử tiếp theo
    - Lập một danh sách và xem qua nó

```js
// Tạo một giỏ hàng mới
const cart = new ShoppingCart();

// Thêm các sản phẩm vào giỏ hàng
cart.addProduct(100); // Giá của sản phẩm: 100
cart.addProduct(200); // Giá của sản phẩm: 200
cart.addProduct(300); // Giá của sản phẩm: 300

// Tính toán giảm giá
const discountCalculator = new Discount();
const totalDiscount = discountCalculator.calc(cart.products);

// Tổng giảm giá
console.log("Tổng giảm giá:", totalDiscount * 100 + "%");
```

```js
class ShoppingCart {
  constructor() {
    this.products = [];
  }

  addProduct(p) {
    this.products.push(p);
  }
}

class Discount {
  calc(products) {
    let ndiscount = new NumberDiscount();
    let pdiscount = new PriceDiscount();
    let none = new NoneDiscount();
    ndiscount.setNext(pdiscount);
    pdiscount.setNext(none);
    return ndiscount.exec(products);
  }
}

class NumberDiscount {
  constructor() {
    this.next = null;
  }

  setNext(fn) {
    this.next = fn;
  }

  exec(products) {
    let result = 0;
    if (products.length > 3) result = 0.05;

    return result + this.next.exec(products);
  }
}

class PriceDiscount {
  constructor() {
    this.next = null;
  }

  setNext(fn) {
    this.next = fn;
  }

  exec(products) {
    let result = 0;
    let total = products.reduce((a, b) => a + b);

    if (total >= 500) result = 0.1;

    return result + this.next.exec(products);
  }
}

class NoneDiscount {
  exec() {
    return 0;
  }
}
```

14. ### Command
    - Đóng gói một yêu cầu vào một đối tượng riêng biệt
    - Tốt cho việc kiểm tra, phát lại, hoàn tác/làm lại
    - Một phần của CQS/CQRS

```js
const turbine = new Turbine();
const onCommand = new OnCommand(turbine);
const offCommand = new OffCommand(turbine);

const cockpit = new Cockpit(onCommand);

cockpit.execute(); // Bật turbine
console.log(turbine.state); // Output: true

cockpit.command = offCommand;
cockpit.execute(); // Tắt turbine
console.log(turbine.state); // Output: false
```

```js
class Cockpit {
  constructor(command) {
    this.command = command;
  }

  execute() {
    this.command.execute();
  }
}

class Turbine {
  constructor() {
    this.state = false;
  }

  on() {
    this.state = true;
  }

  off() {
    this.state = false;
  }
}

class OnCommand {
  constructor(turbine) {
    this.turbine = turbine;
  }

  execute() {
    this.turbine.on();
  }
}

class OffCommand {
  constructor(turbine) {
    this.turbine = turbine;
  }

  execute() {
    this.turbine.off();
  }
}
```

15. ### Interpreter

    - Chuyển đổi văn bản đầu vào thành cấu trúc hướng đối tượng
    - Được sử dụng bởi trình thông dịch, trình biên dịch, công cụ phân tích tĩnh, v.v.
    - Lý thuyết trình biên dịch là một nhánh riêng biệt của khoa học máy tính

```js
// Tạo các biểu diễn của các giá trị và phép tính
const num1 = new Num(10);
const num2 = new Num(5);
const num3 = new Num(3);

// Tạo các phép tính: 10 + 5
const sum = new Sum(num1, num2);

// Tạo phép tính phức tạp hơn: (10 + 5) - 3
const expression = new Min(sum, num3);

// Đánh giá biểu thức và in kết quả
console.log("Result:", expression.interpret()); // Output: 12
```

```js
class Sum {
  constructor(left, right) {
    this.left = left;
    this.right = right;
  }

  interpret() {
    return this.left.interpret() + this.right.interpret();
  }
}

class Min {
  constructor(left, right) {
    this.left = left;
    this.right = right;
  }

  interpret() {
    return this.left.interpret() - this.right.interpret();
  }
}

class Num {
  constructor(val) {
    this.val = val;
  }

  interpret() {
    return this.val;
  }
}
```

16. ### Iterator

    - Cung cấp giao diện để truy cập các phần tử của một đối tượng tổng hợp
    - Các đối tượng có thể được lặp lại (vòng lặp for)

```js
const elements = ["a", "b", "c"];
const iterator = new Iterator(elements);

while (iterator.hasNext()) {
  console.log(iterator.next());
}
```

```js
class Iterator {
  constructor(el) {
    this.index = 0;
    this.elements = el;
  }

  next() {
    return this.elements[this.index++];
  }

  hasNext() {
    return this.index < this.elements.length;
  }
}
```

17. ### Mediator
    - Cung cấp dịch vụ trung gian giữa hai đối tượng
    - Ví dụ: chuyển tin nhắn, phòng trò chuyện

```js
// Create a new TrafficTower instance
const trafficTower = new TrafficTower();

// Create some airplanes and register them with the traffic tower
const airplane1 = new Airplane({ x: 10, y: 20 }, trafficTower);
const airplane2 = new Airplane({ x: 30, y: 40 }, trafficTower);

// Now you can request positions from any airplane or directly from the traffic tower
const positions1 = airplane1.requestPositions();
console.log("Airplane 1 Positions:", positions1);

const positions2 = trafficTower.requestPositions();
console.log("All Airplanes Positions:", positions2);
```

```js
class TrafficTower {
  constructor() {
    this.airplanes = [];
  }

  requestPositions() {
    return this.airplanes.map((airplane) => {
      return airplane.position;
    });
  }
}

class Airplane {
  constructor(position, trafficTower) {
    this.position = position;
    this.trafficTower = trafficTower;
    this.trafficTower.airplanes.push(this);
  }

  requestPositions() {
    return this.trafficTower.requestPositions();
  }
}
```

18. ### Memento
    - Mang lại mã thông báo đại diện cho trạng thái hệ thống
    - Mã thông báo không cho phép thao tác trực tiếp nhưng có thể được sử dụng trong các API thích hợp

```js
// Sử dụng mẫu Memento
const caretaker = new Caretaker();

// Lưu trạng thái ban đầu
caretaker.addMemento(originator.store("Initial state"));

// Thay đổi trạng thái
const newState = "New state";
console.log("Changing state to:", newState);
caretaker.addMemento(originator.store(newState));

// Khôi phục trạng thái trước đó
const previousState = originator.restore(caretaker.getMemento(0));
console.log("Restored state:", previousState);
```

```js
class Memento {
  constructor(value) {
    this.value = value;
  }
}

const originator = {
  store: function (val) {
    return new Memento(val);
  },
  restore: function (memento) {
    return memento.value;
  },
};

class Caretaker {
  constructor() {
    this.values = [];
  }

  addMemento(memento) {
    this.values.push(memento);
  }

  getMemento(index) {
    return this.values[index];
  }
}
```

19. ### Observer

    - Cho phép thông báo về những thay đổi/diễn biến trong một thành phần

```js
// Tạo một đối tượng sản phẩm mới
const product = new Product();

// Tạo các đối tượng phí và lợi nhuận
const fees = new Fees();
const profit = new Profit();

// Đăng ký các đối tượng phí và lợi nhuận với sản phẩm
product.register(fees);
product.register(profit);

// Thiết lập giá cơ bản cho sản phẩm
product.setBasePrice(100);

// In ra giá sản phẩm sau khi áp dụng phí và lợi nhuận
console.log("Giá sau khi áp dụng phí và lợi nhuận:", product.price);
```

```js
class Product {
  constructor() {
    this.price = 0;
    this.actions = [];
  }

  setBasePrice(val) {
    this.price = val;
    this.notifyAll();
  }

  register(observer) {
    this.actions.push(observer);
  }

  unregister(observer) {
    this.actions = this.actions.filter((el) => !(el instanceof observer));
  }

  notifyAll() {
    return this.actions.forEach((el) => el.update(this));
  }
}

class Fees {
  update(product) {
    product.price = product.price * 1.2;
  }
}

class Proft {
  update(product) {
    product.price = product.price * 2;
  }
}
```

20. ### State
    - Chúng tôi lập mô hình các hệ thống bằng cách có một trong các trạng thái có thể có và sự chuyển đổi giữa các trạng thái này
    - Hệ thống như vậy được gọi là máy trạng thái
    - Các khung đặc biệt tồn tại để điều phối các máy trạng thái

```js
const order = new Order();

console.log(order.state.name); // Output: "waitingForPayment"
order.nextState();
console.log(order.state.name); // Output: "shipping"
order.nextState();
console.log(order.state.name); // Output: "delivered"
```

```js
class OrderStatus {
  constructor(name, nextStatus) {
    this.name = name;
    this.nextStatus = nextStatus;
  }

  next() {
    return new this.nextStatus();
  }
}

class WaitingForPayment extends OrderStatus {
  constructor() {
    super("waitingForPayment", Shipping);
  }
}

class Shipping extends OrderStatus {
  constructor() {
    super("shipping", Delivered);
  }
}

class Delivered extends OrderStatus {
  constructor() {
    super("delivered", Delivered);
  }
}

class Order {
  constructor() {
    this.state = new WaitingForPayment();
  }

  nextState() {
    this.state = this.state.next();
  }
}
```

21. ### Strategy
    - Xác định một thuật toán khung với các chi tiết được người triển khai điền vào
    - Chiến lược sử dụng thành phần ban đầu, phương thức mẫu sử dụng tính kế thừa

```js
const cart = new ShoppingCart(guestStrategy);

cart.setAmount(100); // Thiết lập tổng số tiền trong giỏ hàng là 100

console.log(cart.checkout()); // Output: 100 (không giảm giá)

// Chuyển đổi chiến lược sang premiumStrategy
cart.discount = premiumStrategy;

console.log(cart.checkout()); // Output: 80 (giảm giá 20%)
```

```js
class ShoppingCart {
  constructor(discount) {
    this.discount = discount;
    this.amount = 0;
  }

  checkout() {
    return this.discount(this.amount);
  }

  setAmount(amount) {
    this.amount = amount;
  }
}

function guestStrategy(amount) {
  return amount;
}

function regularStrategy(amount) {
  return amount * 0.9;
}

function premiumStrategy(amount) {
  return amount * 0.8;
}
```

22. ### Template
    - Xác định một thuật toán khung với các chi tiết được người triển khai điền vào
    - Chiến lược sử dụng thành phần ban đầu, phương thức mẫu sử dụng tính kế thừa

```js
const tax1 = new Tax1();
const tax2 = new Tax2();

console.log(tax1.calc(900)); // Output: 910 (không vượt qua 1000, không thay đổi)
console.log(tax1.calc(1100)); // Output: 1220 (vượt qua 1000, áp dụng mức thuế 10%)

console.log(tax2.calc(900)); // Output: 910 (không vượt qua 1000, không thay đổi)
console.log(tax2.calc(1100)); // Output: 1320 (vượt qua 1000, áp dụng mức thuế 20%)
```

```js
class Tax {
  calc(value) {
    if (value >= 1000) value = this.overThousand(value);

    return this.complementaryFee(value);
  }

  complementaryFee(value) {
    return value + 10;
  }
}

class Tax1 extends Tax {
  constructor() {
    super();
  }

  overThousand(value) {
    return value * 1.1;
  }
}

class Tax2 extends Tax {
  constructor() {
    super();
  }

  overThousand(value) {
    return value * 1.2;
  }
}
```

23. ### Vistior
    - Cho phép bổ sung chức năng không xâm phạm vào hệ thống tuyển dụng

```js
const manager = new Manager(5000);
const developer = new Developer(3000);

bonusVisitor(manager);
bonusVisitor(developer);

console.log(manager.bonus); // Output: 10000 (bonus cho Manager là lương * 2)
console.log(developer.bonus); // Output: 3000 (bonus cho Developer là lương)
```

```js
function bonusVisitor(employee) {
  if (employee instanceof Manager) employee.bonus = employee.salary * 2;
  if (employee instanceof Developer) employee.bonus = employee.salary;
}

class Employee {
  constructor(salary) {
    this.bonus = 0;
    this.salary = salary;
  }

  accept(visitor) {
    visitor(this);
  }
}

class Manager extends Employee {
  constructor(salary) {
    super(salary);
  }
}

class Developer extends Employee {
  constructor(salary) {
    super(salary);
  }
}
```

- Các tài liệu tham khảo
  - [Reference - design-patterns of tsabunkar](https://github.com/tsabunkar/design-patterns)!
  - [Reference - design-patterns-JS of fbeline](https://github.com/fbeline/design-patterns-JS)!
  - [Reference - node-js-design-patterns of diegomais](https://github.com/diegomais/node-js-design-patterns)!
