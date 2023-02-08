# React code standards and principles 

Энэхүү documentation нь react app хөгжүүлэхэд ямар стандарт баримтлах мөн ямар зарчим баримтлавал зохих талаар байх болно. 

## Баримтлавал зохих зарчим S.O.L.I.D principles

![SOLID Principles](https://miro.medium.com/max/1191/1*OzwARbvHUg1RlZ7LYyLCrg.png)

- SRP: Single Responsibility Principle
- OCP: Open-Closed Principle
- LSP: Liskov Substitution Principle
- ISP: Interface Segregation Principle
- DIP: Dependency Inversion Principle

### SRP: Single Responsibility Principle
SRP зарчимийн тодорхойлолт: Component бүр өөрийн ганц үүрэгтэй байхаар зохион бүтээх. Энэ нь code-ийг илүү readable, maintainable and scalable болгодог.

**SRP ашиглаагүй component**
```tsx
import axios from "axios";
import { useEffect, useMemo, useState } from "react";
import { Rating } from "react-simple-star-rating";

export function Bad() {
  const [products, setProducts] = useState([]);
  const [filterRate, setFilterRate] = useState(1);

  const fetchProducts = async () => {
    const response = await axios.get("https://fakestoreapi.com/products");

    if (response && response.data) setProducts(response.data);
  };

  useEffect(() => {
    fetchProducts();
  }, []);

  const handleRating = (rate: number) => {
    setFilterRate(rate);
  };

  const filteredProducts = useMemo(
    () => products.filter((product: any) => product.rating.rate > filterRate),
    [products, filterRate]
  );

  return (
    <div className="flex flex-col h-full">
      <div className="flex flex-col justify-center items-center">
        <span className="font-semibold">Minimum Rating </span>
        <Rating initialValue={filterRate} SVGclassName="inline-block" onClick={handleRating} />
      </div>
      <div className="h-full flex flex-wrap justify-center">
        {filteredProducts.map((product: any) => (
          <div className="w-56 flex flex-col items-center m-2 max-w-sm bg-white rounded-lg shadow-md dark:bg-gray-800 dark:border-gray-700">
            <a href="#">
              <img className="p-8 rounded-t-lg h-48" src={product.image} alt="product image" />
            </a>
            <div className="flex flex-col px-5 pb-5">
              <a href="#">
                <h5 className="text-lg font-semibold tracking-tight text-gray-900 dark:text-white">
                  {product.title}
                </h5>
              </a>
              <div className="flex items-center mt-2.5 mb-5 flex-1">
                {Array(parseInt(product.rating.rate))
                  .fill("")
                  .map((_, idx) => (
                    <svg
                      aria-hidden="true"
                      className="w-5 h-5 text-yellow-300"
                      fill="currentColor"
                      viewBox="0 0 20 20"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <title>First star</title>
                      <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
                    </svg>
                  ))}

                <span className="bg-blue-100 text-blue-800 text-xs font-semibold mr-2 px-2.5 py-0.5 rounded dark:bg-blue-200 dark:text-blue-800 ml-3">
                  {parseInt(product.rating.rate)}
                </span>
              </div>
              <div className="flex flex-col items-between justify-around">
                <span className="text-2xl font-bold text-gray-900 dark:text-white">
                  ${product.price}
                </span>
                <a
                  href="#"
                  className="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
                  // onClick={onAddToCart}
                >
                  Add to cart
                </a>
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

**SRP ашиглавал**

Logic үйлдэл болон render хийж байгаа JSX code-ийг component болгон задлах. Хэрэв задалвал **filter.tsx**, **product.tsx** мөн logic үйлдлүүдийг **useProduct.tsx**, **useRateFilter.tsx** гэж custom hook үүсгэн задалсан байна. Product list харуулж байгаа хуудас дараах байдалтай харагдах нь.
```tsx
import { Product } from "./product";
import { Filter, filterProducts } from "./filter";
import { useProducts } from "./hooks/useProducts";
import { useRateFilter } from "./hooks/useRateFilter";

export function Good() {
  const { products } = useProducts();

  const { filterRate, handleRating } = useRateFilter();

  return (
    <div className="flex flex-col h-full">
      <Filter filterRate={filterRate as number} handleRating={handleRating} />
      <div className="h-full flex flex-wrap justify-center">
        {filterProducts(products, filterRate).map((product: any, index: number) => (
          <Product product={product} key={index} />
        ))}
      </div>
    </div>
  );
}
```
**Filter.tsx**
```tsx
import { useState } from "react";
import { Rating } from "react-simple-star-rating";

export function filterProducts(products: any[], rate: number) {
  return products.filter(
    (product: any) => product.rating.rate > rate
  );
}

interface IFilterProps {
  filterRate: number;
  handleRating: (rate: number) => void;
}

export function Filter(props: IFilterProps) {
  const { filterRate, handleRating } = props;

  return (
    <div className="flex flex-col justify-center items-center mb-4">
      <span className="font-semibold">Minimum Rating </span>
      <Rating
        initialValue={filterRate}
        SVGclassName="inline-block"
        onClick={handleRating}
      />
    </div>
  );
}
```
**Product.tsx**
```tsx
interface IProduct {
  id: string;
  title: string;
  price: number;
  rating: { rate: number };
  image: string;
}

interface IProductProps {
  product: IProduct;
}

export function Product(props: IProductProps) {
  const { product } = props;
  const { id, title, price, rating, image } = product;

  return (
    <div className="w-56 flex flex-col items-center m-2 max-w-sm bg-white rounded-lg shadow-md dark:bg-gray-800 dark:border-gray-700">
      <a href="#">
        <img className="p-8 rounded-t-lg h-48" src={image} alt="product image" />
      </a>
      <div className="flex flex-col px-5 pb-5">
        <a href="#">
          <h5 className="text-lg font-semibold tracking-tight text-gray-900 dark:text-white">
            {title}
          </h5>
        </a>
        <div className="flex items-center mt-2.5 mb-5 flex-1">
          {Array(Math.trunc(rating.rate))
            .fill("")
            .map((_, idx) => (
              <svg
                aria-hidden="true"
                className="w-5 h-5 text-yellow-300"
                fill="currentColor"
                viewBox="0 0 20 20"
                xmlns="http://www.w3.org/2000/svg"
              >
                <title>First star</title>
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
              </svg>
            ))}

          <span className="bg-blue-100 text-blue-800 text-xs font-semibold mr-2 px-2.5 py-0.5 rounded dark:bg-blue-200 dark:text-blue-800 ml-3">
            {Math.trunc(rating.rate)}
          </span>
        </div>
        <div className="flex flex-col items-between justify-around">
          <span className="text-2xl font-bold text-gray-900 dark:text-white">${price}</span>
          <a
            href="#"
            className="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
            // onClick={onAddToCart}
          >
            Add to cart
          </a>
        </div>
      </div>
    </div>
  );
}
```
**useProduct.tsx**
```tsx
import axios from "axios";
import { useEffect, useState } from "react";

export const useProducts = () => {
  const [products, setProducts] = useState<any[]>([]);

  const fetchProducts = async () => {
    const response = await axios.get(
      "https://fakestoreapi.com/products"
    );

    if (response && response.data) setProducts(response.data);
  };

  useEffect(() => {
    fetchProducts();
  }, []);

  return { products };
};
```
**useRateFilter.tsx**
```tsx
import { useState } from "react";

export function useRateFilter() {
  const [filterRate, setFilterRate] = useState(1);

  const handleRating = (rate: number) => {
    setFilterRate(rate);
  };

  return { filterRate, handleRating };
}
```
### OCP: Open-Closed Principle
OCP зарчимийн тодорхойлолт: Тухайн component өргөтгөл хийх боломжтой боловч өөрчлөхөд хаалттай байх ёстой. Жишээ нь **BUTTON** component дээр үзвэл. **BUTTON** component нь **Icon** харуулдаг гэж төсөөлье. Icon-ийг харуулахдаа **role** гэдэг prop-оор өөр өөр icon харуулах ёстой. Хэрэв OCP ашиглаагүй бол дараах байдалтай бичигдэх нь.

**Button component without OCP**
```tsx
import { HiOutlineArrowNarrowRight, HiOutlineArrowNarrowLeft } from "react-icons/hi";

interface IButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  text: string;
  role?: "back" | "forward";
}

export function Button(props: IButtonProps) {
  const { text, role } = props;

  return (
    <button
      className="flex items-center font-bold outline-none pt-4 pb-4 pl-8 pr-8 rounded-xl bg-gray-200 text-black"
      {...props}
    >
      {text}
      <div className="m-2">
        {role === "forward" && <HiOutlineArrowNarrowRight />}
        {role === "back" && <HiOutlineArrowNarrowLeft />}
      </div>
    </button>
  );
}
```
Хэрэв 10, 20, 30+ **icon** сонголттой хийе гэвэл role prop-ийн сонголт төдий чинээ нэмэгдэж JSX доторх render төдийн чинээ урт бичиглэлтэй болох нь.

Харин **OCP** ашиглаж бичвэл тухайн icon үзүүлж байгаа хэсгийг **children** prop байдлаар аван дараах байдалтай харагдана.

**Button component with OCP**
```tsx
interface IButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  text: string;
  role?: "back" | "forward" | "main" | "not-found";
  icon?: React.ReactNode;
}

export function Button(props: IButtonProps) {
  const { text, icon } = props;

  return (
    <button
      className="flex items-center font-bold outline-none pt-4 pb-4 pl-8 pr-8 rounded-xl bg-gray-200 text-black"
      {...props}
    >
      {text}
      <div className="m-2">
        {icon}
      </div>
    </button>
  );
}
```

**Button component дуудахдаа**
```tsx
import { Button } from "./button";
import {
  HiOutlineArrowNarrowRight,
  HiOutlineArrowNarrowLeft,
} from "react-icons/hi";

export function OCP() {
  return (
    <div className="flex space-x-10">
      <Button
        text="Go Home"
        icon={<HiOutlineArrowNarrowRight />}
      />
      <Button
        text="Go Back"
        icon={<HiOutlineArrowNarrowLeft />}
      />
    </div>
  );
}
```

Ингэснээр Icon-ий variant нэмэгдэхээс үл хамааран **Button** component-д өөрчлөлт орохгүй юм.
