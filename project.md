# IT 2040 Final Project

### This is my final project for IT 2040. The purpose of this project is to sort through various playlist data, and create a report. You can view the report that is generated under report.txt

Program.cs:

```C#
using System;
using System.Collections.Generic;
using System.IO;

namespace Music_Playlist_Analyzer {
    class Program {
        static void Main(string[] args) {
            if (args.Length != 2) {
                Console.WriteLine("MusicPlaylistAnalyzer <music_playlist_file_path> <report_file_path>");
                Environment.Exit(1);
            }

            string csvDataFilePath = args[0];
            string reportFilePath = args[1];

            List<Song> songs = null; 
            try {
                songs = MusicAnalyzerLoader.Load(csvDataFilePath);
            } catch (Exception e) {
                Console.WriteLine(e.Message);
                Environment.Exit(2);
            }
            
            var report = MusicAnalyzerReport.GenerateText(songs);

            try {
                System.IO.File.WriteAllText(reportFilePath, report);
            } catch (Exception e) {
                Console.WriteLine(e.Message);
                Environment.Exit(3);
            }
        }
    }
}
```

MusicAnalyzerLoader.cs:

```C#
using System;
using System.Collections.Generic;
using System.IO;

namespace Music_Playlist_Analyzer {
    public static class MusicAnalyzerLoader {
        private static int NumItemsInRow = 8;

        public static List<Song> Load(string csvDataFilePath) {
            List<Song> songs = new List<Song>();

            try {
                using (var reader = new StreamReader(csvDataFilePath)) {
                    int lineNumber = 0;
                    while (!reader.EndOfStream) {
                        var line = reader.ReadLine();
                        lineNumber++;
                        if (lineNumber == 1) continue;
                        
                        var values = line.Split('\t');

                        if (values.Length != NumItemsInRow) {
                            throw new Exception($"Row {lineNumber} contains {values.Length} values. It should contain {NumItemsInRow}.");
                        }
                        try {
                            string name = values[0];
                            string artist = values[1];
                            string album = values[2];
                            string genre = values[3];
                            int size = Int32.Parse(values[4]);
                            int time = Int32.Parse(values[5]);
                            int year = Int32.Parse(values[6]);
                            int plays = Int32.Parse(values[7]);
                            
                            Song song = new Song(name, artist, album, genre, size, time, year, plays);
                            songs.Add(song);
                        }
                        catch (FormatException e) {
                            throw new Exception($"Row {lineNumber} contains invalid data. ({e.Message})");
                        }
                    }
                }
            } catch (Exception e) {
                throw new Exception($"Unable to open {csvDataFilePath} ({e.Message}).");
            }
            return songs;
        }
    }
}
```

MusicAnalyzerReport.cs:

```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace Music_Playlist_Analyzer {
    public static class MusicAnalyzerReport {
        public static string GenerateText(List<Song> songs) {
            string report = "*** Music Playlist Report ***\n\n";

            if(songs.Count() < 1) {
                report += "No data available.\n";
                return report;
            }

            report += $"Songs that received 200 or more plays: \n";
            var plays = from song in songs where song.Plays > 200 select song;
            if (plays.Count() > 0) {
                foreach(var x in plays) {
                    report += x.ToString() + "\n";
                }
            }
            else {
                report += "Not available\n";
            }

            report += $"\nNumber of Alternative songs: ";
            var altGenre = from song in songs where song.Genre == "Alternative" select song;
            report += altGenre.Count() + "\n";

            report += $"\nNumber of Hip-Hop/Rap songs: ";
            var rapGenre = from song in songs where song.Genre == "Hip-Hop/Rap" select song;
            report += rapGenre.Count() + "\n";
        
            report += $"\nSongs from the album Welcome to the Fishbowl: \n";
            var album = from song in songs where song.Album == "Welcome to the Fishbowl" select song;
            if (album.Count() > 0) {
                foreach(var x in album) {
                    report += x.ToString() + "\n";
                }
            }
            else {
                report += "Not available\n";
            }

            report += $"\nSongs from before 1970: \n";
            var year = from song in songs where song.Year < 1970 select song;
            if (year.Count() > 0) {
                foreach(var x in year) {
                    report += x.ToString() + "\n";
                }
            }
            else {
                report += "Not available\n";
            }

            report += $"\nSong names longer than 85 characters: \n";
            var songSize = from song in songs where song.Name.Count() > 85 select song;
            if (songSize.Count() > 0) {
                foreach(var x in songSize) {
                    report += x.ToString() + "\n";
                }
            }
            else {
                report += "Not available\n";
            }
            
            var longestSong = from song in songs where song.Size == ((from stats in songs select stats.Size).Max()) select song;
            report += $"\nLongest song: \n";
            if (longestSong.Count() > 0) {
                foreach(var x in longestSong) {
                    report += x.ToString() + "\n";
                }
            }
            else {
                report += "Not available\n";
            }
            return report;
        }
    }
}
```

MusicStats.cs:

```C#

using System;

namespace Music_Playlist_Analyzer {
    public class Song {
        public string Name;
        public string Artist;
        public string Album;
        public string Genre;
        public int Size;
        public int Time;
        public int Year;
        public int Plays;

        public Song(string name, string artist, string album, string genre,
        int size, int time, int year, int plays) {
            Name = name;
            Artist = artist;
            Album = album;
            Genre = genre;
            Size = size;
            Time = time;
            Year = year;
            Plays = plays;
        }

        override public string ToString() {
	        return String.Format("Name: {0}, Artist: {1}, Album: {2}, Genre: {3}, Size: {4}, Time: {5}, Year: {6}, Plays: {7}", 
            Name, Artist, Album, Genre, Size, Time, Year, Plays);
        }
    }
}
```

report.txt:

*** Music Playlist Report ***

Songs that received 200 or more plays: 
Name: Losing Touch, Artist: The Killers, Album: Day & Age (Deluxe Version), Genre: Alternative, Size: 9539055, Time: 253, Year: 2008, Plays: 478
Name: Tranquilize (Feat. Lou Reed), Artist: The Killers, Album: Sawdust, Genre: Alternative, Size: 8405381, Time: 225, Year: 2007, Plays: 433
Name: "Ruby, Don't Take Your Love to Town", Artist: The Killers, Album: Sawdust, Genre: Alternative, Size: 6571031, Time: 185, Year: 2007, Plays: 256

Number of Alternative songs: 39

Number of Hip-Hop/Rap songs: 382

Songs from the album Welcome to the Fishbowl: 
Name: Come Over, Artist: Kenny Chesney, Album: Welcome to the Fishbowl, Genre: Country, Size: 10055955, Time: 249, Year: 2012, Plays: 31
Name: Feel like a Rock Star (with Tim McGraw), Artist: Kenny Chesney, Album: Welcome to the Fishbowl, Genre: Country, Size: 8419705, Time: 208, Year: 2012, Plays: 23
Name: Welcome to the Fishbowl, Artist: Kenny Chesney, Album: Welcome to the Fishbowl, Genre: Country, Size: 8487591, Time: 210, Year: 2012, Plays: 10

Songs from before 1970: 
Name: "Hello, I Love You", Artist: The Doors, Album: The Very Best of The Doors, Genre: Rock, Size: 5438829, Time: 136, Year: 1968, Plays: 5

Song names longer than 85 characters: 
Name: "Do You Mind (feat. Nicki Minaj, Chris Brown, August Alsina, Jeremih, Future & Rick Ross)", Artist: DJ Khaled, Album: Major Key, Genre: Hip-Hop/Rap, Size: 11320079, Time: 325, Year: 2016, Plays: 5

Longest song: 
Name: "Devil, Devil (Prelude: Princess of Darkness)", Artist: Eric Church, Album: The Outsiders, Genre: Country, Size: 19450468, Time: 482, Year: 2014, Plays: 37



