# // Voting smart contract
pragma solidity ^0.8.0;

contract Voting {
    // Candidate structure
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // Mapping of candidate ID to Candidate
    mapping(uint => Candidate) public candidates;
    // Mapping of voter address to boolean indicating if they have voted
    mapping(address => bool) public voters;

    // Array to store candidate IDs
    uint[] public candidateIds;

    // Event to emit when a vote is cast
    event Voted(uint candidateId);

    // Function to add a candidate
    function addCandidate(uint _id, string memory _name) public {
        candidates[_id] = Candidate(_id, _name, 0);
        candidateIds.push(_id);
    }

    // Function to cast a vote
    function vote(uint _candidateId) public {
        require(!voters[msg.sender], "You have already voted.");
        require(_candidateId > 0 && _candidateId <= candidateIds.length, "Invalid candidate ID.");

        candidates[_candidateId].voteCount++;
        voters[msg.sender] = true;

        emit Voted(_candidateId);
    }

    // Function to get total votes for a candidate
    function totalVotes(uint _candidateId) public view returns (uint) {
        require(_candidateId > 0 && _candidateId <= candidateIds.length, "Invalid candidate ID.");
        return candidates[_candidateId].voteCount;
    }
}
// Java code to connect to Ethereum and interact with smart contract
import org.web3j.protocol.Web3j;
import org.web3j.protocol.http.HttpService;
import org.web3j.tx.ClientTransactionManager;
import org.web3j.tx.TransactionManager;
import org.web3j.tx.gas.DefaultGasProvider;
import java.math.BigInteger;
import java.util.concurrent.ExecutionException;

public class VoteManager {
    // Ethereum node URL
    private static final String ETHEREUM_NODE_URL = "https://rinkeby.infura.io/v3/YOUR_INFURA_PROJECT_ID";
    // Smart contract address
    private static final String CONTRACT_ADDRESS = "0x123abc...";
    // Ethereum account private key
    private static final String PRIVATE_KEY = "YOUR_PRIVATE_KEY";

    // Web3j instance
    private Web3j web3j;
    // Transaction manager
    private TransactionManager transactionManager;
    // Voting smart contract
    private Voting votingContract;

    public VoteManager() {
        web3j = Web3j.build(new HttpService(ETHEREUM_NODE_URL));
        transactionManager = new ClientTransactionManager(web3j, PRIVATE_KEY);
        votingContract = Voting.load(CONTRACT_ADDRESS, web3j, transactionManager, new DefaultGasProvider());
    }

    // Function to cast a vote
    public void vote(int candidateId) {
        try {
            votingContract.vote(BigInteger.valueOf(candidateId)).send();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Function to get total votes for a candidate
    public int getTotalVotes(int candidateId) {
        try {
            return votingContract.totalVotes(BigInteger.valueOf(candidateId)).send().intValue();
        } catch (Exception e) {
            e.printStackTrace();
            return -1;
        }
    }
}
