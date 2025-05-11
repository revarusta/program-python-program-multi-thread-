# program-python-program-multi-thread-

Berikut adalah contoh program Python sederhana yang menggunakan 
multi-threading untuk mensimulasikan akses cache dan pesan koherensi:

import threading
import time
import random

# Cache simulator
class Cache:
    def __init__(self):
        self.data = {}

    def get(self, key):
        if key in self.data:
            return self.data[key]
        else:
            return None

    def set(self, key, value):
        self.data[key] = value

# Protokol koherensi
class ProtokolKoherensi:
    def __init__(self):
        self.lock = threading.Lock()

    def akses_cache(self, cache, key, value=None):
        with self.lock:
            if value is not None:
                cache.set(key, value)
            return cache.get(key)

# Simulator
class Simulator:
    def __init__(self):
        self.cache = Cache()
        self.protokol_koherensi = ProtokolKoherensi()

    def akses_cache_dengan_koherensi(self, key, value=None):
        return self.protokol_koherensi.akses_cache(self.cache, key, value)

    def akses_cache_tanpa_koherensi(self, key, value=None):
        if value is not None:
            self.cache.set(key, value)
        return self.cache.get(key)

# Thread untuk mengakses cache
class AksesCacheThread(threading.Thread):
    def __init__(self, simulator, key, value=None, dengan_koherensi=True):
        threading.Thread.__init__(self)
        self.simulator = simulator
        self.key = key
        self.value = value
        self.dengan_koherensi = dengan_koherensi

    def run(self):
        if self.dengan_koherensi:
            self.simulator.akses_cache_dengan_koherensi(self.key, self.value)
        else:
            self.simulator.akses_cache_tanpa_koherensi(self.key, self.value)

# Main program
def main():
    simulator = Simulator()

    # Dengan koherensi
    threads = []
    start_time = time.time()
    for i in range(10):
        thread = AksesCacheThread(simulator, f"key{i}", f"value{i}")
        threads.append(thread)
        thread.start()

    for thread in threads:
        thread.join()

    end_time = time.time()
    print(f"Dengan koherensi: {end_time - start_time} detik")

    # Tanpa koherensi
    threads = []
    start_time = time.time()
    for i in range(10):
        thread = AksesCacheThread(simulator, f"key{i}", f"value{i}", dengan_koherensi=False)
        threads.append(thread)
        thread.start()

    for thread in threads:
        thread.join()

    end_time = time.time()
    print(f"Tanpa koherensi: {end_time - start_time} detik")

if __name__ == "__main__":
    main()

Program ini mensimulasikan akses cache dengan dan tanpa protokol koherensi menggunakan multi-threading. Hasilnya akan menunjukkan perbedaan performa antara keduanya.

Namun perlu diingat bahwa program ini hanya contoh sederhana dan tidak sepenuhnya mensimulasikan kondisi nyata. Performa sebenarnya dapat dipengaruhi oleh banyak faktor, seperti jumlah thread, ukuran cache, dan kompleksitas protokol koherensi.
