// JDBC connection
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class DatabaseHandler {
    private static final String URL = "jdbc:mysql://localhost:3306/NumberGuessGame";
    private static final String USERNAME = "root";
    private static final String PASSWORD = "sivashankar@123"; // Use your password

    public Connection connect() throws Exception {
        return DriverManager.getConnection(URL, USERNAME, PASSWORD);
    }

    public void saveResult(String name, int timeTaken, int moves, int score) {
        String query = "INSERT INTO Results (name, time_taken, moves, score) VALUES (?, ?, ?, ?)";
        try (Connection conn = connect(); PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setString(1, name);
            stmt.setInt(2, timeTaken);
            stmt.setInt(3, moves);
            stmt.setInt(4, score);
            stmt.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public String getBestPlayer() {
        String query = "SELECT name, MIN(score) AS bestScore FROM Results GROUP BY name ORDER BY bestScore LIMIT 1";
        try (Connection conn = connect(); PreparedStatement stmt = conn.prepareStatement(query)) {
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                return rs.getString("name");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "No Best Player Yet";
    }
}
