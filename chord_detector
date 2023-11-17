import mido
from mido import Message
from pychord import find_chords_from_notes
import tkinter as tk

# tkinter stuff
root = tk.Tk()
root.title("Chord Detector")

chord_label = tk.Label(root, text="Chord: ", font=('Helvetica', 32))
chord_label.pack(pady=20)

notes_label = tk.Label(root, text="Notes: ", font=('Helvetica', 24))
notes_label.pack(pady=10)

piano_frame = tk.Frame(root)
piano_frame.pack()

# piano key visuals
white_keys = ['C', 'D', 'E', 'F', 'G', 'A', 'B']
black_keys = ['C#', 'D#', '', 'F#', 'G#', 'A#', '']

white_key_labels = {}
for i in range(7):  
    key = tk.Label(piano_frame, text=white_keys[i], width=5, height=8, relief='raised', borderwidth=1, bg='white', fg='black')
    key.grid(row=1, column=i*2, columnspan=2, padx=1, pady=1)
    white_key_labels[white_keys[i]] = key

black_key_labels = {}
for i in range(7):  
    if black_keys[i]:
        key = tk.Label(piano_frame, text=black_keys[i], width=3, height=4, relief='raised', borderwidth=1, bg='black', fg='white')
        key.grid(row=0, column=i*2+1, padx=1, pady=1)
        black_key_labels[black_keys[i]] = key

# update piano keys so they turn gray when pressed
def update_piano_keys(notes):
    for note, key_label in white_key_labels.items():
        if note in notes:
            key_label.config(bg='gray')
        else:
            key_label.config(bg='white')
    for note, key_label in black_key_labels.items():
        if note in notes:
            key_label.config(bg='gray')
        else:
            key_label.config(bg='black')

# is the window open lol
is_window_open = True

# function to update text
def update_display(chord_name, notes):
    if is_window_open:
        chord_label['text'] = "Chord: " + chord_name
        notes_label['text'] = "Notes: " + " ".join(notes)
        update_piano_keys(notes)
        root.update_idletasks()

# function to close window
def close_window():
    global is_window_open
    is_window_open = False
    root.quit()
    root.destroy()

# quit button
quit_button = tk.Button(root, text="Quit", command=close_window, font=('Helvetica', 16))
quit_button.pack(pady=20)

# converting midi to note
def note_number_to_pitch_class(note_number):
    scale = ['C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#', 'A', 'A#', 'B']
    return scale[note_number % 12]

# now that the prep is done, time to cook

#first you have to check if there is even a MIDI input connected to the computer
try:
    # list all the MIDI input ports
    midi_input_names = mido.get_input_names()
    print(midi_input_names)
    
    # check if there are any MIDI input ports
    if not midi_input_names:
        print("No MIDI input detected. Please make sure your MIDI keyboard is connected and turned on.")
        input("Press Enter to exit...")
        close_window()  # closes the program if MIDI isn't detected
    else:
        # open the first MIDI input port
        with mido.open_input(midi_input_names[0]) as inport:
            print('Using MIDI input:', inport)
            active_notes = set()

            # keeps the program alive and updates for midi inputs
            while is_window_open:
                # tkinter
                root.update()

                for msg in inport.iter_pending():
                    note_pitch_class = note_number_to_pitch_class(msg.note)
                    if msg.type == 'note_on' and msg.velocity > 0:
                        active_notes.add(note_pitch_class)
                    elif msg.type == 'note_off' or (msg.type == 'note_on' and msg.velocity == 0):
                        if note_pitch_class in active_notes:
                            active_notes.remove(note_pitch_class)

                    # sorts the notes, or else pychord will get confused and not understand how chords work
                    sorted_notes = sorted(active_notes)

                    if sorted_notes:
                        # uses pychord to search chord data base and cross reference with the sorted_notes
                        possible_chords = find_chords_from_notes(sorted_notes)
                        chord_name = str(possible_chords[0]) if possible_chords else "No chord detected"
                    else:
                        chord_name = "No chord"
                    
                    # update the display with the detected chord and notes
                    update_display(chord_name, sorted_notes)

except Exception as e:
        print(e)

# start the Tkinter loop
root.mainloop()
