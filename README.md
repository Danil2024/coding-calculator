# coding-calculator
# Function: from any base to 10-th
def to_dec(num, base):
    digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    num = num.upper()
    result = 0
    
    for digit in num:
        value = digits.index(digit)
        result = result * base + value
    
    return result

# Function: from 10-th to any base
def from_dec(num, base):
    digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    
    if num == 0:
        return "0"
    
    result = ""
    while num > 0:
        result = digits[num % base] + result
        num //= base
    
    return result

# Function: Elias gamma encoding
def elias_gamma_encode(num):
    if num <= 0:
        return "Invalid input: number must be positive"
    
    binary = bin(num)[2:]
    zeros = '0' * (len(binary) - 1)
    return zeros + binary

# Function: Elias gamma decoding
def elias_gamma_decode(code):
    zero_count = 0
    for char in code:
        if char == '0':
            zero_count += 1
        else:
            break
    
    expected_length = 2 * zero_count + 1
    if len(code) != expected_length:
        return f"Invalid code: expected length {expected_length}, got {len(code)}"
    
    binary_part = code[zero_count:]
    return int(binary_part, 2)

# Function: RLE encoding
def rle_encode(text):
    if not text:
        return ""
    
    result = ""
    count = 1
    current_char = text[0]
    
    for i in range(1, len(text)):
        if text[i] == current_char:
            count += 1
        else:
            result += f"{count}{current_char}"
            current_char = text[i]
            count = 1
    
    result += f"{count}{current_char}"
    return result

# Function: RLE decoding
def rle_decode(encoded_text):
    if not encoded_text:
        return ""
    
    result = ""
    i = 0
    
    while i < len(encoded_text):
        count_str = ""
        while i < len(encoded_text) and encoded_text[i].isdigit():
            count_str += encoded_text[i]
            i += 1
        
        if i >= len(encoded_text):
            break
            
        char = encoded_text[i]
        i += 1
        
        if count_str:
            count = int(count_str)
            result += char * count
    
    return result

# Function: Base64 encoding
def base64_encode(data):
    """Encode string to Base64"""
    # Base64 alphabet
    base64_chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
    
    # Convert string to bytes
    if isinstance(data, str):
        data_bytes = data.encode('utf-8')
    else:
        data_bytes = data
    
    result = ""
    padding = 0
    
    # Process 3 bytes at a time
    for i in range(0, len(data_bytes), 3):
        chunk = data_bytes[i:i+3]
        
        # Convert to binary
        binary_chunk = ""
        for byte in chunk:
            binary_chunk += format(byte, '08b')
        
        # Add padding if needed
        if len(chunk) < 3:
            padding = 3 - len(chunk)
            binary_chunk += '00' * padding
        
        # Split into 6-bit groups
        for j in range(0, len(binary_chunk), 6):
            bits = binary_chunk[j:j+6].ljust(6, '0')
            index = int(bits, 2)
            result += base64_chars[index]
    
    # Add padding characters
    if padding > 0:
        result = result[:-padding] + '=' * padding
    
    return result

# Function: Base64 decoding
def base64_decode(encoded_data):
    """Decode Base64 string"""
    base64_chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
    
    # Remove padding and count padding characters
    padding = encoded_data.count('=')
    encoded_data = encoded_data.rstrip('=')
    
    binary_string = ""
    
    # Convert each character to 6-bit binary
    for char in encoded_data:
        if char in base64_chars:
            index = base64_chars.index(char)
            binary_string += format(index, '06b')
    
    # Remove padding bits
    if padding:
        binary_string = binary_string[:-padding*2]
    
    # Convert binary to bytes
    result_bytes = bytearray()
    for i in range(0, len(binary_string), 8):
        byte_str = binary_string[i:i+8]
        if len(byte_str) == 8:
            result_bytes.append(int(byte_str, 2))
    try:
        return result_bytes.decode('utf-8')
    except:
        return f"Binary data: {result_bytes}"

#Regular Calculator
def regular_calculator():
    print("\n=== REGULAR CALCULATOR ===")
    print("Operations: +, -, *, /, %, ** (power)")
    print("Enter 'exit', 'quit', or 'q' to return to main menu")
    
    while True:
        try:
            expression = input("\nEnter expression (e.g., 2 + 3 * 4): ").strip()
            
            # Check for exit commands FIRST (before any other processing)
            exit_commands = ['exit', 'quit', 'q', 'menu']
            if expression.lower() in exit_commands:
                print("Returning to main menu...")
                return  # Use return instead of break for clarity
            
            if not expression:
                continue
            
            # Safety check - allow only numbers, operators, parentheses and spaces
            allowed_chars = set("0123456789+-*/.%() ")
            if not all(c in allowed_chars for c in expression.replace("**", "")):
                print("Error: Invalid characters in expression")
                continue
            
            # Evaluate the expression safely
            result = eval(expression)
            print(f"Result: {result}")
            
        except ZeroDivisionError:
            print("Error: Division by zero!")
        except SyntaxError:
            print("Error: Invalid expression syntax")
        except NameError:
            print("Error: Expression contains invalid characters")
        except Exception as e:
            print(f"Error: {e}")

# Converter Program
def converter_program():
    print("\n=== CONVERTER with ELIAS GAMMA, RLE and BASE64 ===")
    
    while True:
        print("\n1. Convert to 10-th")
        print("2. Convert from 10-th")
        print("3. Elias Gamma encode")
        print("4. Elias Gamma decode")
        print("5. RLE encode")
        print("6. RLE decode")
        print("7. Base64 encode")
        print("8. Base64 decode")
        print("9. Return to main menu")
        
        choice = input("Choose (1-9): ")
        
        if choice == "1":
            num = input("Number: ").upper()
            base = int(input("From base (2-36): "))
            result = to_dec(num, base)
            print(f"Result: {result}")
            
        elif choice == "2":
            num = int(input("Decimal number: "))
            base = int(input("To base (2-36): "))
            result = from_dec(num, base)
            print(f"Result: {result}")
            
        elif choice == "3":
            num_str = input("Enter number to encode: ")
            if num_str.isdigit():
                num = int(num_str)
                if num > 0:
                    encoded = elias_gamma_encode(num)
                    print(f"Gamma code: {encoded}")
                else:
                    print("Error: Number must be greater than 0")
            else:
                print("Error: Please enter a valid number")
                
        elif choice == "4":
            code = input("Enter gamma code: ")
            if all(c in '01' for c in code):
                decoded = elias_gamma_decode(code)
                if isinstance(decoded, str):
                    print(f"Error: {decoded}")
                else:
                    print(f"Decoded number: {decoded}")
            else:
                print("Error: Gamma code must contain only 0 and 1")
                
        elif choice == "5":
            text = input("Enter text to encode: ")
            if text:
                encoded = rle_encode(text)
                print(f"RLE encoded: {encoded}")
            else:
                print("Error: Text cannot be empty")
                
        elif choice == "6":
            encoded = input("Enter RLE code (format: 3A2B1C): ")
            if encoded:
                decoded = rle_decode(encoded)
                print(f"Decoded text: {decoded}")
            else:
                print("Error: RLE code cannot be empty")
                
        elif choice == "7":
            text = input("Enter text to Base64 encode: ")
            if text:
                encoded = base64_encode(text)
                print(f"Base64 encoded: {encoded}")
            else:
                print("Error: Text cannot be empty")
                
        elif choice == "8":
            encoded = input("Enter Base64 string: ")
            if encoded:
                decoded = base64_decode(encoded)
                print(f"Decoded: {decoded}")
            else:
                print("Error: Base64 string cannot be empty")
            
        elif choice == "9":
            print("Returning to main menu...")
            break
            
        else:
            print("Invalid choice")

# Main Program
def main():
    print("                  CALCULATOR v1.0        ")
    
    while True:
        print("\nMAIN MENU")
        print("1. Regular Calculator (arithmetic operations)")
        print("2. Converter (number systems, encoding, compression)")
        print("3. Exit")
        
        choice = input("Choose (1-3): ")
        
        if choice == "1":
            regular_calculator()
            
        elif choice == "2":
            converter_program()
            
        elif choice == "3":
            print("\nThank you for using Calculator! Goodbye!")
            break
            
        else:
            print("Invalid choice. Please enter 1, 2 or 3.")

# Run program
if __name__ == "__main__":
    main()
