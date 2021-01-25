---
title: C++模板初试及一个基本的complex的实现
categories: 
- C++
- 侯捷老师视频
date: 2021-01-25 23:25:05
tags:
- C++
- 侯捷
- 模板
- 泛型编程
- complex
description: 第一次尝试使用模板进行编程并实现了一个基本的复数类
---







complex.hpp

```c++
//
// Created by robos on 2021/1/24.
//

#ifndef C___PRIMER_5TH_COMPLEX_HPP
#define C___PRIMER_5TH_COMPLEX_HPP
#include <ostream>

template<typename T>
class complex {
public:
    explicit complex(const T& real = 0, const T& image = 0)
        : real_(real),
          image_(image){};

    void real(const T& real) { real_ = real; }
    void image(const T& image) { image_ = image; }

    T real() const { return real_; }
    T image() const { return image_; }

    //TODO 其他类型在左边二复数型在右边的 +=,-=,/=,*=,但是这样做似乎或涉及到类型转换的问题，考虑一下还是不做了
    complex<T>& operator+=(const complex<T>& r);
    complex<T>& operator+=(const T& r);
    complex<T>& operator-=(const complex<T>& r);
    complex<T>& operator-=(const T& r);
    complex<T>& operator*=(const complex<T>& r);
    complex<T>& operator*=(const T& r);
    complex<T>& operator/=(const complex<T>& r);
    complex<T>& operator/=(const T& r);

private:
    T real_, image_;
    template<typename U>//TODO 为什么_doapl函数不能直接使用T模板？
    friend complex<U>& _doapl(complex<U>* ths, const complex<U>& r);
};

template<typename U>
inline complex<U>& _doapl(complex<U>* ths, const complex<U>& r) {
    ths->real_ += r.real_;
    ths->image_ += r.image_;

    return *ths;
}

template<typename T>
inline complex<T>& complex<T>::operator+=(const complex<T>& r) {

    return _doapl(this, r);
}

template<typename T>
complex<T>& complex<T>::operator+=(const T& r) {
    this->real_ += r;

    return *this;
}

//template<typename T>
//complex<T>& operator+=(const T& x, const complex<T>& y){
//
//}

template<typename T>
complex<T>& complex<T>::operator-=(const complex<T>& r) {
    this->real_ += r.real_;
    this->image_ += r.image_;

    return *this;
}

template<typename T>
complex<T>& complex<T>::operator-=(const T& r) {
    this->real_ -= r;

    return *this;
}

template<typename T>
complex<T>& complex<T>::operator*=(const complex<T>& r) {
    this->real_ = this->real_ * r.real_ - this->image_ * r.image_;
    this->image_ = this->real_ * r.image_ + this->image_ * r.real_;

    return *this;
}

template<typename T>
complex<T>& complex<T>::operator*=(const T& r) {
    this->real_ *= r;
    this->image_ *= r;

    return *this;
}

template<typename T>
complex<T>& complex<T>::operator/=(const complex<T>& r) {
    this->real_ = (this->real_ * r.real_ + this->image_ * r.image_) /
                  (r.image_ * r.image_ + r.real_ * r.real_);
    this->image_ = (this->image_ * r.real_ - this->real_ * r.image_) /
                   (r.image_ * r.image_ + r.real_ * r.real_);

    return *this;
}

template<typename T>
complex<T>& complex<T>::operator/=(const T& r) {
    this->real_ /= r;
    this->image_ /= r;

    return *this;
}

template<typename T>
inline complex<T> operator+(const complex<T>& x, const complex<T>& y) {

    return complex<T>(x.real() + y.real(), x.image() + y.image());
}

template<typename T>
inline complex<T> operator+(const T& x, const complex<T>& y) {

    return complex<T>(x + y.real(), y.image());
}

template<typename T>
inline complex<T> operator+(const complex<T>& x, const T& y) {

    return complex<T>(x.real() + y, x.image());
}

template<typename T>
inline bool operator==(const complex<T>& x, const complex<T>& y) {

    return (x.real() == y.real()) && (x.image() == y.image());
}

template<typename T>
inline bool operator==(const complex<T>& x, const T& y) {

    return (x.real() == y) && (x.image() == T());
}

template<typename T>
inline bool operator==(const T& x, const complex<T>& y) {

    return (x = y.real()) && (y.image() == T());
}

template<typename T>
inline complex<T> operator-(const complex<T>& x, const complex<T>& y) {

    return complex<T>(x.real() - y.real(), x.image() - y.image());
}

template<typename T>
inline complex<T> operator-(const T& x, const complex<T>& y) {

    return complex<T>(x + y.real(), y.image());
}

template<typename T>
inline complex<T> operator-(const complex<T>& x, const T& y) {

    return complex<T>(x.real() + y, x.image());
}

template<typename T>
inline complex<T> operator*(const complex<T>& x, const complex<T>& y) {

    return complex<T>(x.real() * y.real() - x.image() * y.image(),
                      x.real() * y.image() + x.image_ * y.real());
}

template<typename T>
inline complex<T> operator*(const complex<T>& x, const T& y){

    return complex<T>(x.real() * y, x.image() * y);
}

template<typename T>
inline complex<T> operator*(const T& x, const complex<T>& y){

    return complex<T>(x * y.real(), x * y.image());
}

template<typename T>
inline complex<T> operator/(const complex<T>& x, const complex<T>& y) {

    return complex<T>((x.real() * y.real() + x.image() * y.image())/
                              (y.real() * y.real() + y.image() * y.image()),
                      (x.image() * y.image() - x.real() * y.image())/
                              (y.real() * y.real() + y.image() * y.image()));
}

template<typename T>
inline complex<T> operator/(const complex<T>& x, const T& y) {

    return complex<T>(x.real() / y, x.image() / y);
}

template<typename T>
inline complex<T> operator/(const T& x, const complex<T>& y){

    return complex<T>((x * y.real()) / (y.real() * y.real() + y.image() * y.image()),
                      (-x * y.image()) / (y.real() * y.real() + y.image() * y.image()));
}

template<typename T>
inline complex<T> conj(const complex<T>& x) {

    return complex<T>(x.real(), -x.image());
}

template<typename T>
std::ostream& operator<<(std::ostream& os, const complex<T> x) {

    return os << '(' << x.real() << ',' << x.image() << ')';
}
#endif//C___PRIMER_5TH_COMPLEX_HPP

```

