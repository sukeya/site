# try_lock_shared_until
* shared_mutex[meta header]
* std[meta namespace]
* shared_timed_mutex[meta class]
* function template[meta id-type]
* cpp14[meta cpp]

```cpp
template <class Clock, class Duration>
bool try_lock_shared_until(const chrono::time_point<Clock, Duration>& abs_time);
```
* time_point[link /reference/chrono/time_point.md]

## 概要
タイムアウトする絶対時間を指定して共有ロックの取得を試みる。


## 要件
この関数を呼び出したスレッドが、ミューテックスの排他所有権と共有所有権のいずれもを保持していないこと。


## テンプレートパラメータ制約
- [`chrono::is_clock_v`](/reference/chrono/is_clock.md)`<Clock>`が`true`であること (C++20)


## 効果
`abs_time`パラメータで指定された絶対時間に到達するまで、ミューテックスの共有所有権の取得を試みる。

共有所有権が取得できるまで、もしくは`abs_time`時間に到達するまでこの関数はブロッキングする。

`abs_time`にすでに到達していた場合、この関数は[`try_lock_shared()`](try_lock_shared.md)と同じ効果をもち、ブロッキングせずにミューテックスの共有所有権の取得を試みる。


## 戻り値
共有所有権が取得できた場合は`true`を返す。

`abs_time`パラメータで指定された相対時間の間に共有所有権が取得できなかった場合はタイムアウトとなり、`false`を返す。


## 例外
時計クラス、[`time_point`](/reference/chrono/time_point.md)クラス、[`duration`](/reference/chrono/duration.md)クラスの構築が例外を送出する場合、この関数はそれらの例外を送出する。


## 例
```cpp example
#include <thread>
#include <shared_mutex>

class X {
  mutable std::shared_timed_mutex mtx_;
  int value_ = 0;
public:
  // メンバ変数value_への書き込みを排他的にする
  void add_value(int value)
  {
    mtx_.lock(); // 排他ロックを取得する
    value_ = value;
    mtx_.unlock(); // 排他ロックを手放す
  }

  // メンバ変数value_の値を読み込む
  int get_value() const
  {
    int result = 0;

    namespace chrono = std::chrono;

    chrono::steady_clock::time_point tp = chrono::steady_clock::now();

    // 共有ロックの取得を試みる(3秒でタイムアウト)
    if (!mtx_.try_lock_shared_until(tp + std::chrono::seconds(3))) {
      // 共有ロックの取得に失敗
      std::error_code ec(static_cast<int>(std::errc::device_or_resource_busy), std::generic_category());
      throw std::system_error(ec);
    }

    result = value_;
    mtx_.unlock_shared(); // 共有ロックを手放す
    return result;
  }
};

int main()
{
  X x;

  std::thread t1([&x]{ x.add_value(1); int value = x.get_value(); });
  std::thread t2([&x]{ x.add_value(2); int value = x.get_value(); });

  t1.join();
  t2.join();
}
```
* try_lock_shared_until[color ff0000]
* lock()[link lock.md]
* unlock()[link unlock.md]
* unlock_shared()[link unlock_shared.md]
* chrono::steady_clock[link /reference/chrono/steady_clock.md]
* now()[link /reference/chrono/steady_clock/now.md]
* std::errc::device_or_resource_busy[link /reference/system_error/errc.md]
* std::generic_category()[link /reference/system_error/generic_category.md]
* std::system_error[link /reference/system_error/system_error.md]

### 出力
```
```

## バージョン
### 言語
- C++14

### 処理系
- [Clang](/implementation.md#clang): 3.5 [mark verified]
- [GCC](/implementation.md#gcc): 4.9 [mark verified]
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): 2015 [mark verified]
