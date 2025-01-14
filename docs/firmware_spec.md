# Firmware Specification

## MCU

    STM32

## IDE & Tool

    Arduino 
    STM32IDE with FreeRTOS

## Language

    C & C++

## Function Description

    Main Function
    Support Function

## IO Define

| Pin      |      Function      |  IO   |
|----------|:------------------:|------:|
| PA0      |      Rest          |  Input   |

## Data Structure

``` c
struct person_t {
    char *name;
    unsigned age;
};
```

## Detail Function Block

    hardware_init
    driver
    user_io_fn
    comm_io_fn
    main_algorithem
    log_fn