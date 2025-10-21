# nslookup
mengecek DNS (Domain Name System)
import subprocess
import requests
import time

def get_ip_from_nslookup(domain):
    print(f"\n🔍 Melakukan nslookup ke {domain}...\n")
    try:
        result = subprocess.run(["nslookup", domain], capture_output=True, text=True)
        output = result.stdout

        # Cari baris yang berisi 'Address:'
        lines = [line for line in output.split("\n") if "Address:" in line and "#" not in line]

        if len(lines) > 1:
            ip_address = lines[-1].split("Address:")[-1].strip()
            print(f"🌐 IP ditemukan: {ip_address}")
            return ip_address
        else:
            print("❌ Tidak menemukan alamat IP dari nslookup.")
            return None
    except Exception as e:
        print(f"Terjadi kesalahan: {e}")
        return None

def get_geo_info(ip):
    print(f"\n🌎 Mengambil informasi lokasi untuk {ip}...\n")
    try:
        response = requests.get(f"https://ipinfo.io/{ip}/json", timeout=5)
        data = response.json()

        city = data.get("city", "Tidak diketahui")
        region = data.get("region", "Tidak diketahui")
        country = data.get("country", "Tidak diketahui")
        org = data.get("org", "Tidak diketahui")
        loc = data.get("loc", "Tidak diketahui")

        print("📍 Informasi Lokasi:")
        print(f"   🌆 Kota      : {city}")
        print(f"   🏙️  Wilayah  : {region}")
        print(f"   🇨🇴 Negara    : {country}")
        print(f"   🏢 Provider  : {org}")
        print(f"   📡 Koordinat : {loc}")

    except requests.exceptions.RequestException:
        print("❌ Gagal mengambil data lokasi. Pastikan koneksi internet aktif.")

def main():
    print("===== 🌐 DNS Visual Mapper =====")
    domain = input("Masukkan nama domain (contoh: google.com): ").strip()

    start_time = time.time()
    ip = get_ip_from_nslookup(domain)

    if ip:
        get_geo_info(ip)
        duration = time.time() - start_time
        print(f"\n⏱️ Waktu total proses: {duration:.2f} detik")
    else:
        print("\n❌ Gagal mendapatkan IP dari domain tersebut.")

if __name__ == "__main__":
    main()
