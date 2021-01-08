/*
 * ....................Buda Bizden Olsun....................
 *
 * https://github.com/projelerim-arduino/Buda-Bizden-Olsun
 * 
 * YOUTUBE Kanalımız 
 * 
 * https://www.youtube.com/channel/UCVnVbX4pFV01Nu7zUbV8exA
 * 
 */
 /**********************************************************************************
   DS1302'nin Arduino'ya Baplantı Pinleri.
   DS1302:  CE pin    -> Arduino Digital 2  (RST)
            I/O pin   -> Arduino Digital 3  (DAT)
            SCLK pin  -> Arduino Digital 4  (CLK)
**********************************************************************************/
/**********************************************************************************
     IMPORTANT: LCDWIKI_KBV LIBRARY MUST BE SPECIFICALLY
     CONFIGURED FOR EITHER THE TFT SHIELD OR THE BREAKOUT BOARD.
  when using the BREAKOUT BOARD only and using these 16 data lines to the LCD,
  pin usage as follow:
               CS  CD  WR  RD  RST  D0  D1  D2  D3  D4  D5  D6  D7  D8  D9  D10  D11  D12  D13  D14  D15 
  Arduino Mega 40  38  39  /   41   37  36  35  34  33  32  31  30  22  23  24   25   26   27   28   29

  Remember to set the pins to suit your display module!
**********************************************************************************/
// Ekran için ihtiyacımız olan kütüphane

#include <LCDWIKI_GUI.h> 
#include <LCDWIKI_KBV.h> 

LCDWIKI_KBV my_lcd(ILI9486,40,38,39,-1,41); //model,cs,cd,wr,rd,reset

#define BLACK   0x0000
#define BLUE    0x001F
#define RED     0xF800
#define GREEN   0x07E0
#define CYAN    0x07FF
#define MAGENTA 0xF81F
#define YELLOW  0xFFE0
#define WHITE   0xFFFF

#include <DS1302.h>
// DS1302'nin Arduino'da Bağlayacağımız Pin Atamaları.
DS1302 rtc(A15, A14, A13);   // (RST, DATA, CLK)

uint16_t color_mask[] = { 0x001F, 0x07E0, 0xF800, 0xFFFF,0x07FF,0xF81F,0xFFE0 };

void show_string(uint8_t *str,int16_t x,int16_t y,uint8_t csize,uint16_t fc, uint16_t bc,boolean mode)
{
    my_lcd.Set_Text_Mode(mode);
    my_lcd.Set_Text_Size(csize);
    my_lcd.Set_Text_colour(fc);
    my_lcd.Set_Text_Back_colour(bc);
    my_lcd.Print_String(str,x,y);
}

void setup()
{
  my_lcd.Init_LCD();
  my_lcd.Fill_Screen(BLACK);

 // Burada Saati Çalışma Moduna Ayarlayıp ve Yazma Korumasını Devre Dışı Bırakmalıyız.
 rtc.halt(false);
 rtc.writeProtect(false);

 /**********************************************************************************
    İlk yüklememizde aşağıdaki üç satırı aktive edip DS1302'ye gercek zamanı yazmamız gerekmektedir.
    Doğru çalıştığını gördüğümüzde, aşağıdaki satırları pasiv edip tekrar göndermeliyiz.
    Aksi takdirde gücü kesip verdiğinizde yada kapatıp açtığınızda zaman ayarınız yanlış olur.
    DS1302 Kütüphanesinde değişiklik yapılmıştır. Orjinal kütüphane kullanıldığında;
    rtc.setDOW ingilizce tanımı büyük harf ile yazılmalıdır.
    rtc.setDate kısmında 8.01.2021 şeklinde yazılmalkıdır.    
**********************************************************************************/

/*
 rtc.setDOW(Cuma);          // Haftanın gününü "Pazartesi" olarak ayarladık.
 rtc.setTime(18, 25,00);       // Saati 24 saat formatında mevcut zamanı yazmamız gerekiryor.
 rtc.setDate(8, Ocak, 2021);  // Tarihi olarak mevcut tarihi yazmamız gerekiyor.

*/
}

void loop() 
{
    uint16_t max_scroll,rotation,i,n;
    my_lcd.Set_Rotation(0);
    uint16_t scrollbuf[my_lcd.Get_Display_Height()]; 
    
    delay(100);
    for(rotation = 0;rotation<4;rotation++)
    {
        my_lcd.Set_Rotation(1);
        my_lcd.Fill_Screen(BLACK);   
            
            delay(100);

        //my_lcd.Set_Text_Back_colour(WHITE);
        my_lcd.Set_Text_colour(RED);
        my_lcd.Set_Text_Size(3); 
        my_lcd.Print_String("Buda Bizden Olsun", CENTER, 10);
  
          // Tarihi TFT Ekrana Gönderiyouz 
        my_lcd.Set_Text_colour(WHITE);
        my_lcd.Set_Text_Size(3);
        my_lcd.Print_String(rtc.getDateStr(), 10, 120);

          // Hsftanın Gününü TFT Ekrana Gönderiyouz
        my_lcd.Set_Text_colour(WHITE);
        my_lcd.Set_Text_Size(3);
        my_lcd.Print_String(rtc.getDOWStr(), 230, 120);
        
            //  "SAATİN" yazısının altındaki renk için
        for(n=0;n<32;n++)
        {
          my_lcd.Set_Draw_color(n*8, n*8, n*8); 
          my_lcd.Set_Draw_color(my_lcd.Get_Draw_color()&color_mask[rotation]);
          my_lcd.Fill_Rectangle(n*my_lcd.Get_Display_Width()/32,48,(n+1)*my_lcd.Get_Display_Width()/32,112);
        }
        show_string(rtc.getTimeStr(),CENTER,70,4,color_mask[rotation+1], BLACK,1);
        
             delay(2500);
         show_string("iYi Ki GECiYORSUN ZAMAN.",0,200,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("YA ACININ EN DERiNiME iSLEDiGi",0,230,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("BiR ANDA DONSAYDIN!",0,260,2,BLUE, BLACK,0);
            delay (2500);
         show_string("MEVLANA",260,280,2,RED, BLACK,0);
            delay(3000);
            
          show_string("                                                   ",0,160,2,BLUE, BLACK,0); 
         show_string("                                                    ",0,190,2,BLUE, BLACK,0); 
         show_string("                                                    ",0,200,2,BLUE, BLACK,0);
         show_string("                                                    ",0,230,2,BLUE, BLACK,0);
         show_string("                                                    ",0,260,2,BLUE, BLACK,0);
         show_string("                                                    ",260,280,2,RED, BLACK,0);
            delay(100);
      
         show_string("SABIR NEDIR? DiKENE BAKIP GULU,",0,160,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("GECEYE BAKIP GUNDUZU, ",0,190,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("TAHAYYUL EDEBiLMEKTiR. ",0,220,2,BLUE, BLACK,0);
            delay (2500);
         show_string("VE BiLiRLER Ki GOKTEKi AYIN HiLAL'DEN",0,250,2,BLUE, BLACK,0);
            delay (2500);
         show_string("DOLUNAYA VARMASI iCiN ZAMAN GEREKiR.",0,280,2,BLUE, BLACK,0);
            delay (2500);
         show_string("SEMS-i TEBRiZi",260,300,2,RED, BLACK,0);
            delay(3000);

         show_string("                                                    ",0,160,2,BLUE, BLACK,0); 
         show_string("                                                    ",0,190,2,BLUE, BLACK,0); 
         show_string("                                                    ",0,220,2,BLUE, BLACK,0);
         show_string("                                                    ",0,250,2,BLUE, BLACK,0);
         show_string("                                                    ",0,280,2,BLUE, BLACK,0);
         show_string("                                                    ",260,300,2,RED, BLACK,0);
            delay(100);
         
         show_string("OLMAZ DEDiGiN NE VAR iSE HEPSi OLUR.",0,170,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("DUSMEM DERSiN DUSERSiN,",0,200,2,BLUE, BLACK,0); 
            delay (2500);
         show_string("SASMAM DERSiN SASARSIN.",0,230,2,BLUE, BLACK,0);
            delay (2500);
            show_string("OLDUM DER DURU. YiNEDE YASARSIN...!!",0,260,2,BLUE, BLACK,0);
            delay (2500);
         show_string("MEVLANA",260,280,2,RED, BLACK,0);
            delay(3000);
            
     }   
}
