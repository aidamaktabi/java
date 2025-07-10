# java
import java.io.*;
import java.util.*;

public class GitLogViewer {
    public static void main(String[] args) {
        try {
            // Run 'git log' with pretty format for hash and message
            ProcessBuilder pb = new ProcessBuilder("git", "log", "--pretty=format:%h|%s", "--abbrev-commit");
            pb.redirectErrorStream(true);
            Process process = pb.start();

            BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()));
            List<Commit> commits = new ArrayList<>();
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split("\\|", 2);
                if (parts.length == 2) {
                    commits.add(new Commit(parts[0], parts[1]));
                }
            }
            process.waitFor();

            System.out.println("Recent Git Commits:");
            System.out.println("-------------------");
            for (Commit commit : commits) {
                System.out.printf("%-10s %s%n", commit.hash, commit.message);
            }
        } catch (Exception e) {
            System.err.println("Error running git log: " + e.getMessage());
        }
    }

    static class Commit {
        String hash, message;
        Commit(String hash, String message) {
            this.hash = hash;
            this.message = message;
        }
    }
}
