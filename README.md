




Danh sách 5-9 Cách chấp nhận được để thay đổi một thành viên dữ liệu của một đối tượng.
/*****************************************************
GHI VÀO CỔNG (Thêm một hàm thành viên để thay đổi
dữ liệu thành viên riêng tư).
Lưu ý: việc cố gắng sử dụng địa chỉ cổng 0x3BC sẽ thất bại
nếu máy tính của bạn không có cổng song song thứ hai.
*****************************************************/
#include <dos.h>
class ParallelPort
{
private:   // Thuộc tính truy cập đã được thay đổi
           // trở lại thành riêng tư.
    unsigned int BaseAddress;
public:
    ParallelPort();
    ParallelPort(int baseaddress);
    void WritePort0(unsigned char data);
    // Hàm thành viên công khai mới được thêm vào.
    void ChangeAddress(unsigned int newaddress);
};
ParallelPort::ParallelPort()
{
    BaseAddress = 0x378;
}
ParallelPort::ParallelPort(int baseaddress)
{
    BaseAddress = baseaddress;
}
void ParallelPort::WritePort0(unsigned char data)
{
    outportb(BaseAddress,data);
}
// Hàm thành viên công khai mới được định nghĩa.
void ParallelPort::ChangeAddress(unsigned int newaddress)
{
    BaseAddress = newaddress;
}
void main()
{
    ParallelPort OurPort;
    // Cách đúng để thao tác một thành viên dữ liệu riêng tư
    OurPort.ChangeAddress(0x3BC);
    OurPort.WritePort0(255);
}


 












 
Danh sách 5-12 Ghi dữ liệu vào cổng ở BASE và đọc dữ liệu từ cổng ở BASE+1.
/*****************************************************
Lớp đối tượng ParallelPort cơ bản được mở rộng để
bao gồm cổng đầu vào ở địa chỉ BASE+1. Đối tượng kết hợp
vẫn được đặt tên là ParallelPort và được sử dụng để ghi
vào cổng ở địa chỉ BASE và để đọc dữ liệu từ cổng
ở địa chỉ BASE+1.
*****************************************************/
#include <stdio.h>
#include <dos.h>
class ParallelPort
{
private:
    unsigned int BaseAddress;
    unsigned char InDataPort1;
public:
    ParallelPort();
    ParallelPort(int baseaddress);
    void WritePort0(unsigned char data);
    void WritePort2(unsigned char data);
    unsigned char ReadPort1();
};
ParallelPort::ParallelPort()
{
    BaseAddress = 0x378;
    InDataPort1 = 0;
}
ParallelPort::ParallelPort(int baseaddress)
{
    BaseAddress = baseaddress;
    InDataPort1 = 0;
}
void ParallelPort::WritePort0(unsigned char data)
{
    outportb(BaseAddress,data);
}
void ParallelPort::WritePort2(unsigned char data)
{
    outportb(BaseAddress+2,data ^ 0x0B);
}
unsigned char ParallelPort::ReadPort1()
{
    InDataPort1 = inportb(BaseAddress+1);
    // Đảo ngược bit quan trọng nhất để bù trừ
    // cho sự đảo ngược bên trong của phần cứng cổng máy in.
    InDataPort1 ^= 0x80;
    // Bộ lọc để xóa các bit dữ liệu không sử dụng D0, D1 và D2 về 0.
    InDataPort1 &= 0xF8;
    return InDataPort1;
}
void main()
{
    unsigned char BASE1Data;
    ParallelPort OurPort;
    OurPort.WritePort0(255);
    BASE1Data = OurPort.ReadPort1();
    printf("\nData Read from Port at BASE+1 %2X\n",BASE1Data);
}


 
Danh sách 5-14 Định nghĩa các hàm thành viên của lớp ParallelPort.

ParallelPort::ParallelPort()
{
    BaseAddress = 0x378;
    InDataPort1 = 0;
}
ParallelPort::ParallelPort(int baseaddress)
{
    BaseAddress = baseaddress;
    InDataPort1 = 0;
}
void ParallelPort::WritePort0(unsigned char data)
{
    outportb(BaseAddress,data);
}

void ParallelPort::WritePort2(unsigned char data)
{
    // Đảo ngược các bit 0, 1 và 3 để bù trừ cho
    // các đảo ngược bên trong của phần cứng cổng máy in.
    outportb(BaseAddress+2, data ^ 0x0B);
}

unsigned char ParallelPort::ReadPort1()
{
    InDataPort1 = inportb(BaseAddress+1);
    // Đảo ngược bit quan trọng nhất để bù trừ cho
    // sự đảo ngược bên trong của phần cứng cổng máy in.
    InDataPort1 ^= 0x80;
    // Bộ lọc để xóa các bit dữ liệu không sử dụng D0, D1 và D2 về 0.
    InDataPort1 &= 0xF8;
    return InDataPort1;
}

Danh sách 5-15 Các hoạt động nhập và xuất bằng lớp ParallelPort.

/*****************************************************
Lớp đối tượng được tạo để sử dụng các cổng ở địa chỉ
BASE và BASE+1 đã được mở rộng để bao gồm đầu ra
thông qua cổng ở địa chỉ BASE+2. Lớp đối tượng kết hợp
vẫn được đặt tên là ParallelPort.
*****************************************************/
#include <dos.h>
#include <conio.h>
#include <stdio.h>
class ParallelPort
{
private:
    unsigned int BaseAddress;
    unsigned char InDataPort1;
public:
    ParallelPort();
    ParallelPort(int baseaddress);
    void WritePort0(unsigned char data);
    void WritePort2(unsigned char data);
    unsigned char ReadPort1();
};
ParallelPort::ParallelPort()
{
    BaseAddress = 0x378;
    InDataPort1 = 0;
}
ParallelPort::ParallelPort(int baseaddress)
{
    BaseAddress = baseaddress;
    InDataPort1 = 0;
}
void ParallelPort::WritePort0(unsigned char data)
{
    outportb(BaseAddress,data);
}
void ParallelPort::WritePort2(unsigned char data)
{
    outportb(BaseAddress+2,data ^ 0x0B);
}
unsigned char ParallelPort::ReadPort1()
{
    InDataPort1 = inportb(BaseAddress+1);
    // Đảo ngược bit quan trọng nhất để bù trừ
    // cho sự đảo ngược bên trong của phần cứng cổng máy in.
    InDataPort1 ^= 0x80;
    // Bộ lọc để xóa các bit dữ liệu không sử dụng D0, D1 và D2 về 0.
    InDataPort1 &= 0xF8;
    return InDataPort1;
}
void main()
{
    unsigned char BASE1Data;
    ParallelPort OurPort;
    OurPort.WritePort0(0x55);
    printf("\n\nData sent to Port at BASE\n");
    getch();
    BASE1Data = OurPort.ReadPort1();
    printf("\nData read from Port at BASE+1: %2X\n",
    BASE1Data);
    getch();
    OurPort.WritePort2(0x00);
    printf("\nData sent to Port at BASE+2\n");
    getch();
}

