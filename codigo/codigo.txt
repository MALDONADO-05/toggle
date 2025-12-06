#define F_CPU 16000000UL          // Frecuencia del oscilador: 16 MHz
#include <avr/io.h>               // Librería para acceso a registros del AVR
#include <util/delay.h>           // Librería para retardos

int main(void){
    // Configuración del Puerto D mediante registro DDR, salidas (LED en PD4) y entradas (Pulsador en PD0)
    DDRD = 0b11110000;
    
    // Configuración del registro PORT para activar resistencias pull-up en los pines de entrada
    PORTD = 0b00001111;
   _delay_ms(10);
    
    // Variable que almacena el estado anterior del botón inicializada en 1 sin presionar
    uint8_t estadoAnterior = 1;
    
    // Variable que controla el estado del LED donde 0 = Apagado, 1 = Encendido
    uint8_t estadoLED = 0;
    
    
    while (1){
       
        uint8_t estadoActual = (PIND & 0b00000001);  // Lectura del estado actual del botón mediante registro pin y se aplica máscara AND para leer (PD0)
        
        
        if (estadoAnterior && !estadoActual) { // Detección de flanco descendente cuando el boton esta presionando 
            
            estadoLED = !estadoLED; // Alternancia del estado del LED
            
            if (estadoLED) { // Enciende el LED, usando operación OR para no alterar otros bits
                PORTD |= 0b00010000;
            } else { //de locontrario
                
                PORTD &= 0b11101111; // Apaga el LED, usando operación AND con máscara invertida
            }
        }
        
       
        estadoAnterior = estadoActual;  // Actualización del estado anterior para la siguiente 
        
        
        
        _delay_ms(50); // Retardo antirrebote: espera 50 ms para evitar lecturas
    }
}